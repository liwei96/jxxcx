<template>
	<view>
		<!-- <view class="toptitle" @tap="back">
			<view class="status_bar">
			</view>
			<image src="../../static/all-back.png" mode=""></image>
		</view>
		<view class="topbb">

		</view> -->
		<image class="topimg" src="../../static/other/forward-top.jpg" mode=""></image>
		<view class="txt">
			已有<text>781</text>人成功预约免费专车看房
		</view>
		<view class="tit">
			您的称呼:
		</view>
		<view class="radio">
			<view class="item" @click="sex = 0">
				<image :src="sex === 1 ? normal : active" mode=""></image>
				<text>先生</text>
			</view>
			<view class="item" @click="sex = 1">
				<image :src="sex === 0 ? normal : active" mode=""></image>
				<text>女士</text>
			</view>
		</view>
		<view class="tit">
			预约看房时间:
		</view>
		<picker mode="date" :value="date" start="starttime" @change="bindDateChange" fields="day">
			<view class="box">
				<view>{{date}}</view>
				<image src="../../static/other/help-go.png" mode=""></image>
			</view>
		</picker>
		<view class="tit">
			想看的楼盘<text>（非必填）</text>
		</view>
		<view class="box" @tap="gosearch">
			<view v-html="name"></view>
			<image src="../../static/other/help-go.png" mode=""></image>
		</view>
		<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber" v-if="!pass&&!weixin">
			<view class="btn">
				确定
			</view>
		</button>
		<view class="btn" v-if="pass||weixin" @tap="show(1)">
			确定
		</view>
		<popup ref="popup" type="center" height="438" width="578" radius="12" closeIconPos="top-right"
			:showCloseIcon="true" closeIconSize="32" @hide="setback">
			<view class="popup-content">
				<view class="tit">
					预约看房
				</view>
				<view class="one" v-if="!iscode">
					<view class="input-box">
						<input type="text" value="" placeholder="请输入手机号" placeholder-class="txt" maxlength="11"
							v-model="tel" :disabled="isok == 1" />
						<view class="close" v-if="isok ==1" @tap="setnull">
							x
						</view>
					</view>
					<view class="msg">
						<check :checkBoxSize='20' :fontSize='18' :checked="true" @change="change"></check>
						<view class="kk">我已阅读并同意<text @tap="goserve">《家园服务协议》</text></view>
					</view>
					<view class="yes" @tap="send">
						确定
					</view>
				</view>
				<view class="two" v-if="iscode">
					<view class="codemsg">
						验证码已发送到{{teltxt}} 请注意查看
					</view>
					<view class="input-box">
						<input type="text" value="" placeholder="请输入验证码" placeholder-class="txt" maxlength="11"
							v-model="code" />
						<text @tap="sendmsg">{{timetxt}}</text>
					</view>
					<view class="yes" @tap="sure">
						确定
					</view>
				</view>
			</view>
		</popup>
		<toast ref="toast" :txt="toasttxt"></toast>
	</view>
</template>

<script>
	import wybPopup from '@/components/wyb-popup/wyb-popup.vue'
	import toast from '@/components/mytoast/mytoast.vue'
	import wycheckbox from '@/components/jiuai-checkbox/jiuai-checkbox.vue'
	var that
	export default {
		components: {
			"toast": toast,
			"popup": wybPopup,
			"check": wycheckbox
		},
		data() {
			return {
				date: '2020',
				time: '请选择您要预约的看房时间',
				sex: 0,
				normal: '../../static/other/forward-normal.png',
				active: '../../static/other/forwark-active.png',
				iscode: false,
				name: '选择楼盘',
				checked: true,
				toasttxt: '',
				tel: '',
				code: '',
				teltxt: '',
				timetxt: '',
				time: 0,
				date: '',
				isok: 0,
				pass: false,
				weixin: false,
				timer: null
			}
		},
		onLoad() {
			that = this
			// #ifdef  MP-WEIXIN
			// this.weixin = true
			// #endif
			this.pass = uni.getStorageSync('pass')
			if (this.pass) {
				this.isok = 1
			}
			this.tel = uni.getStorageSync('phone')
			let name = uni.getStorageSync('searchname')
			if (name) {
				this.name = name
			}
			//#ifdef MP-BAIDU
			swan.setPageInfo({
				title: '家园新房-预约看房',
				keywords: '家园新房-预约看房',
				description: '家园新房-预约看房',
				success: res => {
					console.log('setPageInfo success', res);
				},
				fail: err => {
					console.log('setPageInfo fail', err);
				}
			})
			//#endif
		},
		methods: {
			register(pro) {
				let uuid = uni.getStorageSync('uuid')
				let city = uni.getStorageSync('city')
				let ip = uni.getStorageSync('ip')
				let arr = getCurrentPages()
				let url = arr[arr.length - 1].route
				let host = this.host
					url=url+'?id='+pro+'&host='+host+'&uuid='+uuid+'&kid='+uni.getStorageSync('kid')+'&other='+uni.getStorageSync('other')+'&plan='+uni.getStorageSync('plan')+'&unit='+uni.getStorageSync('unit')+'&semwords='+uni.getStorageSync('semwords')
				let pp = {
					controller: "Info",
					action: "register",
					params: {
						city: city,
						project: pro,
						ip: ip,
						url: url,
						uuid: uuid,
						host: host
					},
				};
				this.$store.state.socket.send({
					data: JSON.stringify(pp)
				});
			},
			show(n) {
				// #ifdef  MP-WEIXIN
				// this.isok = 0
				// #endif
				this.$refs.popup.show()
			},
			setnull() {
				this.isok = 0
				this.tel = ''
			},
			async getPhoneNumber(e) {
				console.log(e)
				let that = this
				// #ifdef  MP-BAIDU
				if (e.detail.errMsg == 'getPhoneNumber:fail auth deny') {
					this.isok = 0
				} else {
					this.pass = true
					uni.setStorageSync('pass', true)
					this.isok = 1
					let session = uni.getStorageSync('session')
					if (session) {
						uni.request({
							url: "https://java.edefang.net/applets/jy/decrypt",
							method: "post",
							data: {
								iv: e.detail.iv,
								ciphertext: e.detail.encryptedData,
								sessionKey: session,
								other: uni.getStorageSync("other"),
								uuid: uni.getStorageSync("uuid"),
							},
							header: {
								"Content-Type": "application/x-www-form-urlencoded;charset=utf-8",
							},
							success: (res) => {
								console.log(res);
								let tel = res.data.data.mobile;
								uni.setStorageSync('phone', tel)
								let openid = uni.getStorageSync('openid')
								that.tel = tel
							}
						})
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
										uni.setStorageSync("session", res.data.data
											.session_key);
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
														console.log(
															res);
														uni.setStorageSync(
															"token",
															res
															.data
															.data);
													},
												})
											}
										})

									}
								})
							}
						});
					}
				}
				// #endif
				// #ifdef  MP-WEIXIN
				if (e.detail.errMsg != 'getPhoneNumber:ok') {
					this.isok = 0
				} else {
					this.pass = true
					uni.setStorageSync('pass', true)
					this.isok = 1
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
											let token = uni.getStorageSync('token')
											if (!token) {
												let openid = uni.getStorageSync(
													'openid')
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
														console.log(
															res)
														uni.setStorageSync(
															'token',
															res
															.data
															.token)
													}
												})
											}
											uni.setStorageSync('phone', tel)
											that.tel = tel
										}
									})

								}
							})
						}
					});
				}
				// #endif
				this.$refs.popup.show()
			},
			back() {
				uni.navigateBack({
					data: 1
				})
			},
			send() {
				console.log(this.checked)
				if (!this.checked) {
					that.toasttxt = '请选择用户协议'
					that.$refs.toast.show()
					return
				}
				var phone = this.tel;
				var pattern_phone = /^1[3-9][0-9]{9}$/;
				if (phone == "") {
					this.toasttxt = "请输入手机号";
					this.$refs.toast.show()
					return;
				} else if (!pattern_phone.test(phone)) {
					this.toasttxt = "手机号码不合法";
					this.$refs.toast.show()
					return;
				}
				let kid = uni.getStorageSync('kid') || ''
				let other = uni.getStorageSync('other') || ''
				let ip = ''
				let city = uni.getStorageSync('city') || 1
				let txt = `客户预约看房时间为${that.date}`;
				let sex = ''
				if (that.sex == 0) {
					sex = '先生'
				} else {
					sex = '女士'
				}
				let id = uni.getStorageSync('searchid') || 0
				uni.request({
					url: that.putserve + '/getIp.php',
					method: 'GET',
					success: (res) => {
						ip = res.data
						ip = ip.split('=')[1].split(':')[1]
						ip = ip.substring(1)
						ip = ip.slice(0, -3)
						uni.request({
							url: that.putserve + '/front/flow/sign',
							data: {
								city: city,
								page: 11,
								project: id,
								remark: txt,
								source: '线上推广1',
								ip: ip,
								position: 110,
								tel: phone,
								kid: kid,
								other: other,
								name: sex,
								other: uni.getStorageSync('other'),
								uuid: uni.getStorageSync('uuid'),
								site: 1,
								device: 3
							},
							method: "GET",
							success: (res) => {
								if (res.data.code == 500) {
									that.toasttxt = res.data.message;
									that.$refs.toast.show()
									setTimeout(() => {
										that.$refs.popup.hide()
									}, 1500)
								} else {
									that.teltxt = phone.substr(0, 3) + "****" + phone.substr(7,
										11);
									that.iscode = true
									that.time = 60
									clearInterval(that.timer);
									var fn = function() {
										that.time--;
										if (that.time > 0) {
											that.istime = true
											that.timetxt = `等待${that.time}s`
										} else {
											that.istime = false
											clearInterval(that.timer);
											that.timetxt = `获取验证码`
										}
									};
									fn();
									that.timer = setInterval(fn, 1000);

									if (that.isok == 1) {
										that.toasttxt = "订阅成功";
										that.$refs.toast.show()
										that.iscode = false
										that.$refs.popup.hide()
										if (!uni.getStorageSync('token')) {
											let openid = uni.getStorageSync('openid')
											uni.request({
												url: "https://api.edefang.net/applets/login",
												method: 'GET',
												data: {
													phone: phone,
													openid: openid,
													other: uni.getStorageSync('other'),
													uuid: uni.getStorageSync('uuid')
												},
												success: (res) => {
													console.log(res)
													uni.setStorageSync('token', res
														.data.token)
												}
											})
										}
									} else {
										that.iscode = true
									}
								}

							}
						})
						console.log(that.isok)
						if (that.isok != 1) {
							uni.request({
								url: that.apiserve + '/send',
								method: "POST",
								data: {
									ip: ip,
									phone: phone,
									source: 3,
									other: uni.getStorageSync('other'),
									uuid: uni.getStorageSync('uuid')
								},
								header: {
									'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
								},
								success: (res) => {
									console.log(res)
								}
							})
						}
					}
				})
			},
			sure() {
				if (!this.code) {
					this.toasttxt = "请输入验证码";
					this.$refs.toast.show()
					return;
				}
				var phone = this.tel;
				let code = this.code
				uni.request({
					url: that.apiserve + '/sure',
					method: "POST",
					data: {
						code: code,
						phone: phone,
						source: 3,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					success: (res) => {
						console.log(res)
						if (res.data.code === 500) {
							that.toasttxt = res.data.message;
							that.$refs.toast.show()
							this.$refs.popup.hide()
						} else {
							that.toasttxt = "订阅成功";
							that.$refs.toast.show()
							that.iscode = false
							this.$refs.popup.hide()
							if (!uni.getStorageSync('token')) {
								uni.setStorageSync('token', res.data.token)
								uni.setStorageSync('phone', that.tel)
							}
						}
					}
				})
			},
			change(e) {
				this.checked = e.detail.checked
			},
			bindDateChange: function(e) {
				this.date = e.target.value
				// this.time = e.target.value
				console.log(this.date)
			},
			put() {
				this.$refs.popup.show()
			},
			setback() {
				this.iscode = false
			},
			gosearch() {
				uni.setStorageSync('search', 1)
				uni.navigateTo({
					url: '/pages/searchname/searchname'
				})
			}
		},
		mounted() {
			this.register(0)
			let date = new Date()
			this.starttime = date.getFullYear() + '-' + ((date.getMonth() + 1) > 10 ? (date.getMonth() + 1) : '0' + (date
				.getMonth() +
				1)) + '-' + (date.getDate() > 10 ? date.getDate() : '0' + date.getDate())
			this.date = this.starttime
			console.log(this.date)
		}
	}
</script>

<style lang="less">
	page {
		background: #FFFFFF;
	}

	.toptitle {
		color: #17181A;
		font-size: 32rpx;
		padding: 0 29.88rpx;
		line-height: 87.64rpx;

		.status_bar {
			height: var(--status-bar-height);
			width: 100%;
		}

		image {
			width: 32rpx;
			height: 32rpx;
			margin-right: 14rpx;
			margin-bottom: -4rpx;
		}
	}

	button {
		padding: 0;
		margin: 0;
	}

	button:after {
		border: none;
	}

	.topimg {
		width: 100%;
		height: 200rpx;
	}

	.txt {
		color: #16181A;
		font-size: 32rpx;
		text-align: center;
		margin-top: 48rpx;
		margin-bottom: 90rpx;

		text {
			color: #FF5454;
		}
	}

	.tit {
		color: #16181A;
		font-size: 32rpx;
		margin-bottom: 32rpx;
		padding: 0 30rpx;

		text {
			color: #969799;
			font-size: 20rpx;
		}
	}

	.radio {
		display: flex;
		padding: 0 30rpx;
		margin-bottom: 58rpx;

		.item {
			display: flex;
			align-items: center;
			margin-right: 80rpx;

			image {
				width: 32rpx;
				height: 32rpx;
				margin-right: 48rpx;
			}

			text {
				color: #646566;
				font-size: 32rpx;
			}
		}
	}

	.box {
		border: 1rpx solid #CFD2D4;
		border-radius: 12rpx;
		background-color: #FAFAFA;
		display: flex;
		align-items: center;
		width: 92%;
		margin-left: 4%;
		height: 88rpx;
		margin-bottom: 58rpx;

		view {
			color: #969799;
			font-size: 28rpx;
			margin-left: 28rpx;

			text {
				color: #969799;
				font-size: 20rpx;
			}
		}

		image {
			width: 32rpx;
			height: 32rpx;
			margin-left: auto;
			margin-right: 26rpx;
		}
	}

	.btn {
		width: 92%;
		margin-left: 4%;
		height: 80rpx;
		border-radius: 12rpx;
		background-color: #2AC66D;
		text-align: center;
		line-height: 80rpx;
		color: #FFFFFF;
		font-weight: bold;
		font-size: 30rpx;
		margin-top: 100rpx;
	}

	.popup-content {
		background-color: #FFFFFF;
		border-radius: 12rpx;

		.tit {
			color: #333333;
			font-size: 44rpx;
			font-weight: bold;
			text-align: center;
			margin-top: 38rpx;
			margin-bottom: 38rpx;
		}

		.input-box {
			width: 498rpx;
			height: 100rpx;
			border-radius: 24rpx;
			background-color: #F7F7F7;
			margin-left: 40rpx;
			display: flex;
			align-items: center;
			position: relative;

			.close {
				position: absolute;
				right: 30rpx;
				top: 28rpx;
				font-size: 30rpx;
			}

			.txt {
				color: #969799;
				font-size: 32rpx;
			}

			input {
				font-size: 32rpx;
				margin-left: 24rpx;
			}

			text {
				color: #7495BD;
				font-size: 32rpx;
			}
		}

		.two {
			.input-box {
				margin-bottom: 40rpx;
			}
		}

		.msg {
			margin-bottom: 48rpx;
			margin-left: 40rpx;
			display: flex;
			align-items: center;

			.kk {
				color: #7A7A7A;
				font-size: 24rpx;
				margin-left: 8rpx;
				position: relative;
				top: 6rpx;

				text {
					color: #7495BD;
				}
			}
		}

		.yes {
			width: 498rpx;
			height: 80rpx;
			border-radius: 12rpx;
			text-align: center;
			line-height: 80rpx;
			background-color: #2AC66D;
			color: #FFFFFF;
			font-size: 28rpx;
			font-weight: bold;
			margin-left: 40rpx;
		}

		.codemsg {
			color: #999999;
			font-size: 22rpx;
			margin-bottom: 24rpx;
			margin-left: 40rpx;
		}
	}
</style>
