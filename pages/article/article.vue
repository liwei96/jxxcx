<template>
	<view class="article">
		<!-- <view class="toptitle" @tap="back">
			<view class="status_bar">
			</view>
			<image src="../../static/all-back.png" mode=""></image>
			<text>买房资格</text>
		</view> -->
		<view class="box">
			<view class="tit">
				{{info.title}}
			</view>
			<view class="infos">
				<text>发布：{{info.time}} &nbsp;&nbsp;&nbsp;来源：{{info.source}}</text>
				<text class="right">浏览：{{info.visit_count}}</text>
			</view>
			<view class="txtbox">
				<text>
					摘要：
				</text>
				{{info.description}}
			</view>
			<view class="" v-html="content">
			</view>
			<view class="label">
				<text class="label-tit">标签：</text>
				<text class="label-li" v-for="(item,key) in info.tags" :key="key">{{item}}</text>
			</view>
			<view class="label-icon">
				<image src="../../static/other/article-label.png" mode=""></image>
				<text>{{info.keywords}}</text>
			</view>
			<view class="agree">
				<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber" v-if="!pass&&!weixin">
					<view :class="info.my_like == 0 ? 'agree-box' : 'agree-box active'">
						<image src="../../static/other/article-agree.png" mode=""></image>
						<view class="agree-num">
							{{info.like_count}}
						</view>
					</view>
				</button>
				<view :class="info.my_like == 0 ? 'agree-box' : 'agree-box active'" v-if="pass||weixin" @tap="agree">
					<image src="../../static/other/article-agree.png" mode=""></image>
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
			<!-- 登录弹框 -->
			<wyb-popup ref="login" type="bottom" height="570" width="650" radius="0" :showCloseIcon="true"
				closeIconSize="32" @hide="setiscode">
				<login @login="logined"></login>
			</wyb-popup>
			<infos :others="others" :tit="'大家都在看'"></infos>
		</view>
	</view>
</template>

<script>
	var that
	import infos from '@/components/articles/articles.vue'
	import login from "@/components/login.vue";
	export default {
		onLoad(options) {
			console.log(options)
			that = this
			this.id = options.id
			this.getdata()
			this.pass = uni.getStorageSync('pass')
			// #ifdef  MP-WEIXIN
			// this.weixin = true
			// #endif
		},
		components: {
			infos,
			login
		},
		data() {
			return {
				jkl: '<p>5555555</p>',
				id: 0,
				info: {},
				others: [],
				content: "",
				pass: false,
				weixin: false
			}
		},
		methods: {
			back() {
				uni.navigateBack({
					data: 1
				})
			},
			getdata() {
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
						let data = res.data.data.article.content
						that.content = data.replace(/\<img/gi, '<img style="max-width:100%;height:auto" ');
						that.others = res.data.data.recommends
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
			agree() {
				let token = uni.getStorageSync('token')
				if (!token) {
					let url = '/pages/article/article?id=' + this.id
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
			logined() {
				this.pass = true;
				this.$refs.login.hide();
				this.agree()
			},
			async getPhoneNumber(e) {
				console.log(e)
				let that = this
				let token = uni.getStorageSync('token')
				// #ifdef  MP-BAIDU
				if (e.detail.errMsg == 'getPhoneNumber:fail auth deny') {
					if (!token) {
						this.$refs.login.show();
						// let url = '/pages/article/article?id=' + this.id
						// uni.setStorageSync('backurl', url)
						// uni.navigateTo({
						// 	url: '/pages/login/login'
						// })
					} else {
						that.agree()
					}
				} else {
					this.pass = true
					uni.setStorageSync('pass', true)
					if (token) {
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
										phone: tel,
										openid: openid,
										uuid: uni.getStorageSync("uuid"),
										city: uni.getStorageSync("city"),
									},
									success: (res) => {
										console.log(res);
										uni.setStorageSync("token", res.data.data);
										uni.setStorageSync('phone', tel)
										that.agree()
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
														that.agree()
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
					if (!token) {
						let url = '/pages/info/info?id=' + this.id
						uni.setStorageSync('backurl', url)
						uni.navigateTo({
							url: '/pages/login/login'
						})
					} else {
						that.agree()
					}
				} else {
					this.pass = true
					uni.setStorageSync('pass', true)
					if (token) {
						that.agree()
						return
					}
					uni.login({
						provider: 'weixin',
						success: function(res) {
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
													that.agree()
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
	page {
		background: #FFFFFF;
	}

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
		margin: 0
	}

	button:after {
		border: none;
	}

	.box {
		padding: 0 30rpx;
		// padding-top: 88rpx;

		.tit {
			color: #17191A;
			font-size: 44rpx;
			line-height: 62rpx;
			font-weight: bold;
			margin-top: 28rpx;
			margin-bottom: 38rpx;
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
			color: #323333;
			font-size: 32rpx;
			line-height: 52rpx;

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
				margin-right: 18rpx;
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
					color: #969899;
					font-size: 24rpx;
				}
			}

			.active {
				background-color: #F7F7F7;
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
