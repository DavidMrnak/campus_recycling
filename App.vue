<script>
	import {fakeLogin,getWeiOpenId} from"@/api/index.js"
	export default {
		onLaunch: function() {

			let self=this;
			// 1.wx获取登录用户code
			uni.login({
				provider: 'weixin',
				success: function(loginRes) {
					wx.getSetting({
						success: function(res) {
							if (res.authSetting['scope.userInfo']) {
								uni.getUserInfo({
									provider: 'weixin',
									success: function(infoRes) {
										getApp().globalData.wUserInfo = infoRes.userInfo;
										uni.setStorageSync("WeiUser",true)
										//登录
										let wUserInfo=getApp().globalData.wUserInfo
										let self=this;
										uni.login({
											provider: 'weixin',
											success: function(loginRes) {
												let code = loginRes.code;
												var data = {
													code: code
												};
												getWeiOpenId(data).then(res => {
													if(res.returnCode=="0"){
														console.info(res.result)
														let data={
															username:res.result,
															// username:getApp().globalData.wUserInfo.nickName,
															password:"123456"
														}
														fakeLogin(data).then(res=>{
															if(res.returnCode=="0"){
																getApp().globalData.userInfo=res.result
																uni.setStorageSync("UserInfo",true)
																uni.setStorageSync('token', res.result.token)
																uni.setStorageSync('userId', res.result.userId)
															}
														})
													} 
												})
											  }
										})
										
										
									}
								});
							}else{
								uni.setStorageSync("WeiUser",false)
							}
						}
					})
				}
			});
		},
		globalData:{
			//微信用户
			wUserInfo:{},
			//登录用户
			userInfo:{}
		},
		onShow: function() {

		},
		onHide: function() {

		}
	}
</script>

<style>
@import url("/static/css/base.css");
</style>
