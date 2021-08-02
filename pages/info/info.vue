<template>
	<view class="article" v-if="issure">
		<!-- <view class="toptitle" @tap="back">
			<view class="status_bar">
			</view>
			<image src="../../static/all-back.png" mode=""></image>
			<text>本地楼市</text>
		</view> -->
		<view class="box">
			<view class="tit">
				{{info.title}}
			</view>
			<view class="infos">
				<text>发布：{{info.time}} &nbsp;&nbsp;&nbsp;来源：{{info.source}}</text>
				<text class="right">浏览：{{info.visit_count}}</text>
			</view>
			<view class="txtbox" v-if="info.description">
				<text>
					摘要：
				</text>
				{{info.description}}
			</view>
			<view class="wenbox" v-html="info.content">
			</view>
			<view class="label">
				<text class="label-tit">标签：</text>
				<text class="label-li" v-for="(item,key) in info.tags" :key="key">{{item}}</text>
			</view>
			<view class="build" v-if="build.length != 0">
				<view class="top" @tap="gobuild(build.id)">
					<view class="left">
						<image :src="build.image" mode=""></image>
					</view>
					<view class="right">
						<view class="right-tit">
							{{build.name}}
							<view class="status" v-if="build.state != null">
								{{build.state}}
							</view>
						</view>
						<view class="price">
							<text class="big">{{build.price}}</text>
							<text>元/m²</text>
						</view>
						<view class="right-infos">
							{{build.type}} | {{build.city}}-{{build.country.substr(0,2)}} | {{build.area}}m²
						</view>
						<view class="right-icons">
							<text class="zhuang" v-if="build.decorate">{{build.decorate}}</text>
							<text v-if="build.railway">{{build.railway}}</text>
							<text v-if="build.feature">{{build.feature}}</text>

						</view>
					</view>
				</view>
				<view class="bom">
					<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber($event,build.id,'文章详情页+在线问')"
						v-if="!pass&&!weixin">
						<view class="btn">
							在线问
						</view>
					</button>
					<view class="btn" @tap="show(build.id,'文章详情页+在线问',1)" v-if="pass||weixin">
						在线问
					</view>
					<view class="tel" @tap="call">
						电话咨询
					</view>
				</view>
			</view>
			<view class="label-icon">
				<image src="../../static/other/article-label.png" mode=""></image>
				<text>楼盘导购</text>
			</view>
			<view class="agree">
				<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber" v-if="!pass&&!weixin">
					<view :class="info.my_like == 0 ? 'agree-box' : 'agree-box active'">
						<image v-if="info.my_like == 0" src="../../static/content/no_zan.png" mode=""></image>
						<image v-if="info.my_like == 1" src="../../static/content/zan.png" mode=""></image>
						<view class="agree-num">
							{{info.like_count}}
						</view>
					</view>
				</button>
				<view :class="info.my_like == 0 ? 'agree-box' : 'agree-box active'" v-if="pass||weixin" @tap="agree">
					<image v-if="info.my_like == 0" src="../../static/content/no_zan.png" mode=""></image>
					<image v-if="info.my_like == 1" src="../../static/content/zan.png" mode=""></image>
					<view class="agree-num">
						{{info.like_count}}
					</view>
				</view>
			</view>
			<view class="statement">
				<text>
					免责声明：
				</text>
				凡本站注明
				“来自：XXX(非家园新房)”的资讯稿件和图片作品，系本站转载自其它媒体，转载目的在于信息传递，并不代表本站赞同其观点和对其真实性负责。如有资讯稿件和图片作品的内容、版权以及其它问题的，请联系本站，电话：400-966-9995
			</view>
			<infos :others="others" :tit="'大家都在看'"></infos>
		</view>
		<!-- 登录弹框 -->
		<wyb-popup ref="login" type="bottom" height="570" width="650" radius="0" :showCloseIcon="true"
			closeIconSize="32" @hide="setiscode">
			<login @login="logined" :pid="build.id"></login>
		</wyb-popup>
		<wyb-popup ref="popup" type="center" height="750" width="650" radius="12" :showCloseIcon="true"
			@hide="setiscode">
			<sign :type="codenum" @closethis="setpop" :title="'咨询服务'" :pid="pid" :remark="remark" :position="position"
				:isok="isok"></sign>
		</wyb-popup>
	</view>
</template>

<script>
	import wybPopup from '@/components/wyb-popup/wyb-popup.vue'
	import sign from '@/components/sign.vue'
	import infos from '@/components/articles/articles.vue'
	import login from "@/components/login.vue";
	var that
	export default {
		onLoad(options) {
			that = this
			this.id = options.id
			// this.getinfo()
			this.getdata()
			this.pass = uni.getStorageSync('pass')
			// #ifdef  MP-WEIXIN
			// this.weixin = true
			// #endif
		},
		data() {
			return {
				jkl: '<p>5555555</p>',
				id: 0,
				info: {},
				others: [],
				build: {},
				pid: 0,
				codenum: 1,
				pass: false,
				position: 104,
				isok: 0,
				weixin: false,
				issure: false
			}
		},
		components: {
			sign,
			wybPopup,
			infos,
			login
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
			logined() {
				this.pass = true;
				this.$refs.login.hide();
				this.agree()
			},
			setpop() {
				this.$refs.popup.hide()
			},
			back() {
				uni.navigateBack({
					data: 1
				})
			},
			getdata() {
				uni.showLoading({
					title: "加载中"
				})
				let city = uni.getStorageSync('city')
				let other = uni.getStorageSync('other')
				let token = uni.getStorageSync('token')
				uni.request({
					url: that.javaserve + "/applets/jy/article",
					method: "POST",
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					data: {
						id: that.id,
						city: city,
						other: other,
						token: token,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					success: (res) => {
						console.log(res)
						uni.setStorageSync('city', res.data.data.current_city.id)
						uni.setStorageSync('cityname', res.data.data.current_city.name)
						that.info = res.data.data.article
						that.build = that.info.building
						that.register(that.build.id)
						uni.hideLoading()
						let data = res.data.data.article.content
						that.info.content = data.replace(/\<img/gi,
						'<img style="max-width:100%;height:auto" ');
						that.others = res.data.data.recommends
						this.tel = res.data.data.phone
						that.issure = true
						//#ifdef MP-BAIDU
						let header = res.data.data.header
						swan.setPageInfo({
							title: header.title,
							keywords: header.keywords,
							description: header.description,
							image: [that.info.img],
							success: res => {
								console.log('setPageInfo success', res);
							},
							fail: err => {
								console.log('setPageInfo fail', err);
							}
						})
						//#endif
					}
				})
			},
			go(id) {
				uni.redirectTo({
					url: "/pages/article/article?id=" + id
				})
			},

			gobuild(id) {
				uni.redirectTo({
					url: "/pageA/content/content?id=" + id
				})
			},
			show(id, txt, n) {
				this.pid = id
				this.remark = txt
				this.position = 104
				console.log(this.pid)
				this.$refs.popup.show()
				this.isok = n
				// // #ifdef  MP-WEIXIN
				// this.isok = 0
				// // #endif
			},
			setiscode() {
				this.codenum = 0
			},
			call() {
				uni.makePhoneCall({
					// 手机号
					phoneNumber: that.tel,
					// 成功回调
					success: (res) => {
						console.log('调用成功!')
					},
					// 失败回调
					fail: (res) => {
						console.log('调用失败!')
					}
				});
			},
			agree() {
				let token = uni.getStorageSync('token')
				if (!token) {
					let url = '/pages/info/info?id=' + this.id
					uni.setStorageSync('backurl', url)
					uni.navigateTo({
						url: '/pages/login/login'
					})
					return
				}
				uni.request({
					url: that.javaserve + "/applets/jy/article/like",
					method: 'POST',
					data: {
						id: that.info.id,
						token: token,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					success: (res) => {
						console.log(res)
						if (that.info.my_like == 0) {
							that.info.like_count++
							that.info.my_like = 1
						} else {
							that.info.like_count--
							that.info.my_like = 0
						}
					}
				})
			},
			async getPhoneNumber(e, bid, txt) {
				console.log(e)
				let that = this
				let token = uni.getStorageSync('token')
				// #ifdef  MP-BAIDU
				if (e.detail.errMsg == 'getPhoneNumber:fail auth deny') {
					if (bid) {
						that.show(bid, txt, 0)
					} else {
						if (!token) {
							this.$refs.login.show();
							// let url = '/pages/info/info?id=' + this.id
							// uni.setStorageSync('backurl', url)
							// uni.navigateTo({
							// 	url: '/pages/login/login'
							// })
						} else {
							that.agree()
						}
					}
				} else {
					this.pass = true
					uni.setStorageSync('pass', true)
					if (token && !bid) {
						that.agree()
						return
					}
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
								uni.request({
									url: "https://java.edefang.net/applets/jy/login",
									method: "POST",
									header: {
										"Content-Type": "application/x-www-form-urlencoded;charset=utf-8",
									},
									data: {
										bid:that.build.id,
										phone: tel,
										openid: openid,
										uuid: uni.getStorageSync("uuid"),
										city: uni.getStorageSync("city"),
									},
									success: (res) => {
										console.log(res);
										uni.setStorageSync("token", res.data.data);
										uni.setStorageSync('phone', tel)
										if (bid) {
											that.show(bid, txt, 1)
										} else {
											that.agree()
										}
									}
								})
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
												uni.request({
													url: "https://java.edefang.net/applets/jy/login",
													method: "POST",
													header: {
														"Content-Type": "application/x-www-form-urlencoded;charset=utf-8",
													},
													data: {
														bid:that.build.id,
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
														uni.setStorageSync(
															'phone',
															tel)
														if (bid) {
															that.show(
																bid,
																txt,
																1)
														} else {
															that.agree()
														}
													}
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
					if (bid) {
						that.show(bid, txt, 0)
					} else {
						if (!token) {
							let url = '/pages/info/info?id=' + this.id
							uni.setStorageSync('backurl', url)
							uni.navigateTo({
								url: '/pages/login/login'
							})
						} else {
							that.agree()
						}
					}
				} else {
					this.pass = true
					uni.setStorageSync('pass', true)
					if (token && !bid) {
						that.agree()
						return
					}
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
													uni.setStorageSync(
														'phone',
														tel)
													if (bid) {
														that.show(bid,
															txt, 1)
													} else {
														that.agree()
													}
												}
											})
										}
									})
								}
							})
						}
					});
				}
				// #endif

			}
		}
	}
</script>

<style lang="less">
	.article {
		background-color: #FFFFFF;
	}

	.toptitle {
		color: #17181A;
		font-size: 29.88rpx;
		padding: 0 29.88rpx;
		line-height: 88rpx;
		position: fixed;
		width: 100%;
		top: 0;
		z-index: 9999;
		background-color: #FFFFFF;

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

	.wenbox {
		image {
			width: 100%;
		}
	}

	.box {
		padding: 0 30rpx;

		.tit {
			color: #17191A;
			font-size: 44rpx;
			line-height: 62rpx;
			font-weight: bold;
			padding-top: 26rpx;
			margin-bottom: 32rpx;
		}

		.infos {
			color: #969899;
			font-size: 24rpx;

			.right {
				float: right;
			}
		}

		.txtbox {
			margin-top: 38rpx;
			background-color: #F7F7F7;
			padding: 30rpx;
			color: #646566;
			font-size: 32rpx;
			line-height: 52rpx;
			border-radius: 24rpx;
			text {
				color: #2AC66D;
			}
		}

		.label {
			.label-tit {
				color: #17191A;
				font-size: 34rpx;
			}

			.label-li {
				color: #969899;
				font-size: 34rpx;
				margin-right: 14rpx;
			}
		}

		.build {
			padding: 24rpx 24rpx 36rpx 24rpx;
			box-shadow: 0px 0px 38rpx 2rpx rgba(0, 0, 0, 0.03);
			border-radius: 24rpx;
			margin-top: 48rpx;

			.top {
				display: flex;
				margin-bottom: 32rpx;

				.left {
					image {
						width: 220rpx;
						height: 160rpx;
						border-radius: 12rpx;
						margin-right: 24rpx;
					}
				}

				.right {
					flex: 1;
					position: relative;
					top: -4rpx;

					.right-tit {
						color: #303233;
						font-size: 32rpx;
						font-size: 800;
						margin-bottom: 6rpx;

						.status {
							width: 72rpx;
							height: 34rpx;
							border-radius: 4rpx;
							text-align: center;
							line-height: 34rpx;
							float: right;
							background-color: #E9F7EA;
							color: #20C657;
							font-size: 22rpx;
							margin-top: 4rpx;
						}
					}

					.price {
						color: #FF6040;
						font-size: 26rpx;
						font-weight: 800rpx;

						.big {
							font-size: 32rpx;
						}
					}

					.right-infos {
						color: #646566;
						font-size: 24rpx;
						margin-top: 4rpx;
						margin-bottom: 4rpx;
						display: flex;
					}

					.right-icons {
						text {
							color: #7D7F80;
							font-size: 24rpx;
							padding: 5rpx 10rpx;
							border-radius: 4rpx;
							background-color: #F5F5F5;
							margin-right: 12rpx;
						}

						.zhuang {
							color: #50B2EC;
							background-color: #F0F5F9;
						}
					}
				}
			}

			.bom {
				display: flex;
				flex-direction: row-reverse;

				view {
					width: 140rpx;
					height: 48rpx;
					border-radius: 8rpx;
					text-align: center;
					line-height: 48rpx;
					font-size: 24rpx;
					color: #FFFFFF;
				}

				.btn {
					background: linear-gradient(270deg, #28C567, #81DB85);
					;
					margin-right: 7rpx;
					margin-left: 24rpx;
				}

				.tel {
					background: linear-gradient(270deg, #FF7519, #FFAE3D);
					;
				}
			}
		}

		.label-icon {
			margin-top: 34rpx;

			image {
				width: 32rpx;
				height: 32rpx;
				margin-bottom: -2rpx;
				margin-right: 8rpx;
			}

			text {
				color: #17191A;
				font-size: 34rpx;
			}
		}

		.agree {
			display: flex;
			margin: 40rpx 0rpx;
			justify-content: center;

			.agree-box {
				width: 120rpx;
				height: 120rpx;
				border-radius: 50%;
				display: flex;
				justify-content: center;
				align-items: center;
				border: 1rpx solid #E8E9ED;
				flex-direction: column;

				image {
					width: 32rpx;
					height: 32rpx;
					margin-bottom: 16rpx;
				}

				.agree-num {
					height: 32rpx;
					color: #969899;
					font-size: 24rpx;
					line-height: 32rpx;
				}
			}
			.active {
				.agree-num {
					color: #38916C;
				}
			}
		}

		.statement {
			border-radius: 12rpx;
			border: 2rpx dashed #EDEDED;
			padding: 28rpx;
			color: #969899;
			font-size: 26rpx;
			line-height: 44rpx;
			margin-bottom: 56rpx;

			text {
				color: #323333;
			}
		}
	}
</style>
