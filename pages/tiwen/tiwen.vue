<template>
	<view class="wenhui">
		<!-- <view class="toptitle">
			<view class="status_bar">
			</view>
			<navigator open-type="navigateBack" delta="1" class="nav_top">
				<image src="../../static/all-back.png" mode=""></image>
				<text>我要提问</text>
			</navigator>
		</view> -->
		<view class="tit">
			家园在线咨询师帮您解答
		</view>
		<view class="da_box">
			<textarea placeholder="在这里输入您的问题" class="text_box" v-model="text" maxlength="50"
				placeholder-style="color:#7D7E80;fontSize:30rpx;">
			</textarea>
			<view class="num_range">
				{{text.length}}/50
			</view>
		</view>
		<!-- <button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber"> -->
		<view class="tijiao_btn" @tap="SendTiwen">
			发布提问
		</view>
		<!-- 	</button> -->
		<mytoast :txt="msg" ref="msg"></mytoast>
	</view>
</template>

<script>
	import mytoast from "@/components/mytoast/mytoast.vue"
	export default {
		data() {
			return {
				text: '',
				project_id: "",
				msg: ""
			};
		},
		components: {
			mytoast
		},
		onLoad(option) {
			this.project_id = option.id;
		},
		methods: {
			getPhoneNumber(e) {
				let that = this
				// #ifdef  MP-BAIDU
				if (e.detail.errMsg == 'getPhoneNumber:fail auth deny') {
					this.isok = 0
					let url = '/pages/tiwen/tiwen?id=' + this.project_id
					uni.setStorageSync('backurl', url)
					console.log(url)
				} else {
					let session = uni.getStorageSync('token')
					if (session) {
						that.SendTiwen()
					} else {
						swan.getLoginCode({
							success: (res) => {
								console.log(res.code);
								uni.request({
									url: "https://java.edefang.net/applets/jy/session_key/get",
									method: "get",
									data: {
										code: res.code,
									},
									success: (res) => {
										console.log(res);
										uni.setStorageSync("openid", res.data.data.openid);
										uni.setStorageSync("session", res.data.data.session_key);
										uni.request({
											url: "https://java.edefang.net/applets/jy/decrypt",
											data: {
												ciphertext: e.detail.encryptedData,
												iv: e.detail.iv,
												sessionKey: res.data.data.session_key,
											},
											method: "POST",
											header: {
												"Content-Type": "application/x-www-form-urlencoded;charset=utf-8",
											},
											success: (res) => {
												console.log(res);
												let tel = res.data.data.mobile;
												uni.setStorageSync('phone', tel)
												let openid = uni.getStorageSync(
													'openid')
												that.tel = tel
												uni.request({
													url: "https://java.edefang.net/applets/jy/login",
													method: "POST",
													header: {
														"Content-Type": "application/x-www-form-urlencoded;charset=utf-8",
													},
													data: {
														phone: tel,
														openid: openid,
														uuid: uni
															.getStorageSync(
																"uuid"),
														city: uni
															.getStorageSync(
																"city"),
													},
													success: (res) => {
														console.log(res);
														uni.setStorageSync(
															"token",
															res.data
															.data);
														that.SendTiwen()
													}
												})

											}
										})

									}
								})
							}
						});
					}
					this.isok = 1
				}
				// #endif
				// #ifdef  MP-WEIXIN
				if (e.detail.errMsg != 'getPhoneNumber:ok') {
					this.isok = 0
					let url = '/pages/tiwen/tiwen?id=' + this.project_id
					uni.setStorageSync('backurl', url)
					console.log(url)
				} else {
					let session = uni.getStorageSync('token')
					if (session) {
						that.SendTiwen()
					} else {
						uni.login({
							provider: 'weixin',
							success: function(res) {
								console.log(res.code);
								uni.request({
									url: 'https://ll.edefang.net/api/weichat/jscode2session',
									method: 'get',
									data: {
										code: res.code,
										other: uni.getStorageSync('other'),
										uuid: uni.getStorageSync('uuid')
									},
									success: (res) => {
										console.log(res)
										uni.setStorageSync('openid', res.data.data.openid)
										uni.setStorageSync('session', res.data.data.session_key)
										uni.request({
											url: "https://ll.edefang.net/api/weichat/decryptData",
											data: {
												data: e.detail.encryptedData,
												iv: e.detail.iv,
												sessionKey: res.data.data.session_key,
												other: uni.getStorageSync('other'),
												uuid: uni.getStorageSync('uuid')
											},
											success: (res) => {
												console.log(res)
												let data = JSON.parse(res.data.message)
												let tel = data.purePhoneNumber
												uni.setStorageSync('phone', tel)
												let openid = uni.getStorageSync(
													'openid')
												that.tel = tel
												uni.request({
													url: "https://api.edefang.net/applets/login",
													method: 'GET',
													data: {
														phone: tel,
														openid: openid,
														other: uni
															.getStorageSync(
																'other'),
														uuid: uni
															.getStorageSync(
																'uuid')
													},
													success: (res) => {
														uni.setStorageSync(
															'token',
															res.data
															.token)
														that.SendTiwen()
													}
												})

											}
										})

									}
								})
							}
						});
					}
					this.isok = 1
				}
				// #endif

			},
			SendTiwen() {
				let token = uni.getStorageSync('token');
				let city_id = uni.getStorageSync("city");
				if (this.text.length > 0) {
					uni.request({
						url: this.apiserve + "/jy/ask",
						data: {
							token: token,
							project: this.project_id,
							city: city_id,
							question: this.text,
							other: uni.getStorageSync('other'),
							uuid: uni.getStorageSync('uuid')
						},
						method: "GET",
						success: (res) => {
							if (res.data.code == 200) {
								this.msg = "提交成功";
								this.$refs.msg.show();
								let baseurl = uni.getStorageSync('backurl');
								uni.navigateTo({
									url: baseurl
								})
							} else {
								console.log(res)
							}
						}
					})
				} else {
					this.msg = "提问内容不能为空";
					this.$refs.msg.show();
				}

			}
		}
	}
</script>

<style lang="less">
	page {
		background: #fff;
	}

	button::after {
		border: none;
	}

	.wenhui {
		padding-left: 30rpx;
		padding-right: 30rpx;
		background: #fff;
		box-sizing: border-box;

		.toptitle {
			color: #fff;
			font-size: 29.88rpx;
			line-height: 87.64rpx;
			background-color: #FFF;

			.status_bar {
				height: var(--status-bar-height);
				width: 100%;
			}

			.nav_top {
				image {
					width: 31.87rpx;
					height: 31.87rpx;
					margin-right: 11.95rpx;
					margin-bottom: -3.98rpx;
				}

				text {
					width: 221rpx;
					font-size: 32rpx;
					font-weight: 500;
					color: #17181A;
				}
			}
		}

		.tit {
			font-size: 32rpx;
			color: #272B2E;
			line-height: 88rpx;
			margin-bottom: 20rpx;
		}

		.da_box {
			width: 100%;
			height: 320rpx;
			position: relative;

			.text_box {
				width: 630rpx;
				height: 290rpx;
				background: #F7F7F7;
				padding-top: 30rpx;
				padding-left: 30rpx;
				padding-right: 30rpx;
			}

			.num_range {
				font-size: 24rpx;
				font-weight: 400;
				color: #969799;
				position: absolute;
				right: 20rpx;
				bottom: 17rpx;
			}
		}

		// button{
		// 	padding-left: 0rpx;
		// 	padding-right: 0rpx;
		.tijiao_btn {
			width: 100%;
			height: 80rpx;
			background: #E9F5EE;
			line-height: 80rpx;
			text-align: center;
			font-size: 30rpx;
			font-weight: bold;
			color: #38916C;
			text-align: center;
			margin-top: 70rpx;
			border-radius: 12rpx;
		}

		// }

	}
</style>
