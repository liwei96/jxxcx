<template>
	<view class="bottom_fixed">
		<view class="zixun" @tap="gotalk">
			<image src="../../static/content/bottom.png" mode=""></image>
			<view class="text">
				在线咨询
			</view>
			<view class="num" v-if="num!=0">
			</view>
		</view>
		<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber" v-if="!pass&&!weixin">
			<view class="yuyue_box">
				<image src="../../static/content/yuyue.png" mode=""></image>
				预约看房
			</view>
		</button>
		<view class="yuyue_box" v-if="pass||weixin" @tap="baoMing(remark, point, title, pid,1)">
			<image src="../../static/content/yuyue.png" mode=""></image>
			预约看房
		</view>
		<view class="tel_box" @tap="boTel(telphone)">
			<image src="../../static/content/tel_bot.png" mode=""></image>
			电话咨询
		</view>
		<wyb-popup ref="popup" type="center" height="750" width="650" radius="12" :showCloseIcon="true"
			@hide="setiscode">
			<sign :type="codenum" @closethis="setpop" :title="title_e" :pid="pid_d" :remark="remark_k"
				:position="position_n" :isok="isok"></sign>
		</wyb-popup>
	</view>
</template>

<script>
	import wybPopup from '@/components/wyb-popup/wyb-popup.vue'
	import sign from '@/components/sign.vue'
	export default {
		props: {
			remark: {
				type: String
			},
			point: {
				type: Number
			},
			title: {
				type: String
			},
			pid: {
				type: String
			},
			telphone: {
				type: String
			},
			projectid: {
				type: Number
			}
		},
		// props: ['remark','point','title','pid','telphone','],
		components: {
			sign,
			wybPopup
		},
		data() {
			return {
				codenum: 1,
				title_e: '',
				pid_d: 0,
				remark_k: '',
				position_n: 0,
				isok: 0,
				sid: 0,
				num: 0,
				pass: false,
				weixin: false,
				type: false
			};
		},
		mounted() {
			console.log(this.$store.state, 78979798789,this.pid)
			if (this.pid) {
				this.register(this.pid)
			}
			let that = this
			this.pass = uni.getStorageSync('pass')
			// this.num = uni.getStorageSync('total')
			this.$store.state.socket.onMessage(function(res) {
				if (res.data.indexOf('{') === -1) {
					return
				}
				let data = JSON.parse(res.data)
				if (data.action == 302) {
					that.sid = data.sid
					if (that.type) {
						that.gotalk()
					}
				} else if (data.action == 301) {
					console.log(that.pid, uni.getStorageSync(String(that.pid)))
					if (!uni.getStorageSync(String(that.pid))) {
						that.$store.state.socket.send({
							data: JSON.stringify({
								controller: "Staff",
								action: "info",
								params: {
									uuid: data.fromUserName,
									host: 'www.jy8006.com'
								},
							}),
							success: res => {
								uni.setStorageSync(String(that.pid), data.fromUserName)
							}
						});
					}
					if (String(data.fromUserName).length < 10) {

						that.sid = data.fromUserName
						if (uni.getStorageSync(String(data.fromUserName))) {
							uni.setStorageSync(String(data.fromUserName), parseInt(uni.getStorageSync(String(data
								.fromUserName))) + 1)
						} else {
							uni.setStorageSync(String(data.fromUserName), 1)
						}
						if (uni.getStorageSync('total') && uni.getStorageSync('total') != 'NaN') {
							uni.setStorageSync('total', parseInt(uni.getStorageSync('total')))
							that.num = that.num + 1;
						} else {
							uni.setStorageSync('total', 1)
							that.num = 1;
						}
					}
				} else if (data.action == 206) {
					let msg = {
						usernum: data.num.user_num,
						looknum: data.num.look_num,
						rate: data.num.rate,
						stafftel: data.staff.tel,
						staffname: data.staff.name,
						staffimg: data.staff.img,
						staffid: data.staff.id
					}
					that.$parent.staffmsg = msg
					that.$parent.$refs.talkbox.show()
					console.log(msg)
				}
			})
		},
		methods: {
			setpop() {
				this.$refs.popup.hide()
			},
			gotalk() {
				let id = String(this.sid)
				if (uni.getStorageSync(id)) {
					let num = uni.getStorageSync(id)
					let total = uni.getStorageSync('total')
					total = total - num
					uni.setStorageSync('total', total)
					uni.removeStorageSync(id)
				}
				this.num = 0
				let pid = this.pid
				uni.navigateTo({
					url: '/pages/talk/talk?id=' + id + '&bid=' + pid
				})
			},
			register(pro, type) {
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
				if (type) {
					this.type = true
				}
			},
			setiscode() {
				this.codenum = 0
			},
			boTel(tel) {
				uni.makePhoneCall({
					phoneNumber: tel,
					success: function() {
						console.log('拨打电话', tel);
					} //仅为示例
				});
			},
			baoMing(remark, point, title, pid, n) {
				this.isok = n
				console.log(remark, point, title, pid, n);
				this.pid_d = pid;
				this.position_n = point;
				this.title_e = title;
				this.remark_k = remark;
				this.$refs.popup.show();
			},
			async getPhoneNumber(e) {
				console.log(e)
				let that = this
				// #ifdef  MP-BAIDU
				if (e.detail.errMsg == 'getPhoneNumber:fail auth deny') {
					that.baoMing(that.remark, that.point, that.title, that.pid, 0)
				} else {
					this.pass = true
					uni.setStorageSync('pass', true)
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
								that.baoMing(that.remark, that.point, that.title, that.pid, 1)
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
														bid: that.pid,
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
														that.baoMing(
															that
															.remark,
															that
															.point,
															that
															.title,
															that
															.pid, 1
															)
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
					that.baoMing(that.remark, that.point, that.title, that.pid, 0)
				} else {
					this.pass = true
					uni.setStorageSync('pass', true)
					let session = uni.getStorageSync('session')
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
											that.pass = true
											uni.setStorageSync('pass', that.pass)
											let tel = data.purePhoneNumber
											uni.setStorageSync('phone', tel)
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
											that.tel = tel
											that.baoMing(that.remark, that.point,
												that.title, that.pid, 1)
										}
									})

								}
							})
						}
					});
				}
				// #endif
			}
		},
		watch: {
			pid(news, val) {
				console.log(news, val)
					this.register(news)
			}
		}
	}
</script>

<style lang="less" scoped>
	button {
		padding: 0;
	}

	.bottom_fixed {
		width: 94%;
		height: 109rpx;
		padding-left: 6%;
		padding-top: 19rpx;
		background-color: #fff;
		position: fixed;
		bottom: 0rpx;
		left: 0rpx;
		border-top: none;
		z-index: 1000;
		display: flex;

		//border-top:1rpx solid #fff;
		.zixun {
			font-size: 24rpx;
			font-weight: 500;
			color: #626466;
			float: left;
			margin-right: 40rpx;
			display: flex;
			align-items: center;
			flex-direction: column;
			position: relative;
			padding-top: 6rpx;

			image {
				width: 48rpx;
				height: 42rpx;
				margin-bottom: 12rpx;
			}

			.num {
				width: 26rpx;
				height: 26rpx;
				border-radius: 50%;
				text-align: center;
				line-height: 26rpx;
				background-color: #F34F4F;
				color: #FFFFFF;
				font-size: 20rpx;
				position: absolute;
				top: 0;
				right: 8rpx;
			}
		}

		.tel_box {
			width: 256rpx;
			height: 88rpx;
			background: linear-gradient(270deg, #FF4B2D, #FFB753);
			border-radius: 8rpx;
			float: left;

			image {
				width: 36rpx;
				height: 36rpx;
				margin-right: 6rpx;
			}

			font-size: 32rpx;
			font-weight: bold;
			color: #FFFFFF;
			display: flex;
			align-items: center;
			justify-content: center;
		}

		button::after {
			border: none;
		}

		.yuyue_box {
			width: 256rpx;
			height: 88rpx;
			background: linear-gradient(270deg, #28C567, #81DB85);
			border-radius: 8rpx;
			float: left;
			margin-right: 20rpx;

			image {
				width: 36rpx;
				height: 36rpx;
				margin-right: 6rpx;
			}

			font-size: 32rpx;
			font-weight: bold;
			line-height: 88rpx;
			color: #FFFFFF;
			display: flex;
			align-items: center;
			justify-content: center;
		}
	}
</style>
