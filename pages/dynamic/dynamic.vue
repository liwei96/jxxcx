<template>
	<view class="dynamic">
		<!-- <view class="toptitle" @tap="back">
			<view class="status_bar">
			</view>
			<image src="../../static/all-back.png" mode=""></image>
			<text>楼盘动态</text>
		</view>
		<view class="topbb">

		</view> -->
		<image src="../../static/dynamic/dynamic-top.jpg" mode="" class="topimg"></image>
		<view class="box">
			<view class="item" v-for="item in list" :key="item.id">
				<view class="top" @tap="go(item.id)">
					<image :src="item.project.image" mode=""></image>
					<view class="zhao"></view>
					<view class="topicon">
						最新
					</view>
					<view class="name">
						{{item.project.name}}
					</view>
					<view class="msg">
						<text>{{item.project.country.substr(0,2)}}</text>
						<text>面积 {{item.project.area}}m²</text>
						<text>均价：{{item.project.price}}元/m²</text>
					</view>
				</view>
				<view class="bom">
					<view class="name">{{item.title}}</view>
					<text class="txt">{{item.content}}</text>
					<view class="time">
						{{item.time}}
					</view>
					<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber($event,item.project.id)"
						v-if="!pass&&!weixin">
						<view class="btn">
							订阅此楼盘动态
						</view>
					</button>
					<view class="btn" v-if='pass||weixin' @tap="show(item.project.id,'动态页+订阅楼盘动态',1)">
						订阅此楼盘动态
					</view>
				</view>
			</view>
		</view>
		<bom-nav :tel="'400-718-6686'" @show="nav"></bom-nav>
		<wyb-popup ref="popup" type="center" height="750" width="650" radius="12" :showCloseIcon="true"
			@hide="setiscode">
			<sign :type="codenum" @closethis="setpop" :title="title" :pid="pid" :remark="remark" :position="position"
				:isok="isok"></sign>
		</wyb-popup>
	</view>
</template>

<script>
	import bomnav from "@/components/bomnav.vue"
	import wybPopup from '@/components/wyb-popup/wyb-popup.vue'
	import sign from '@/components/sign.vue'
	var that
	export default {
		components: {
			"bom-nav": bomnav,
			sign,
			wybPopup
		},
		onShow() {
			that = this
			this.getdata()
		},
		onLoad(options) {
			this.city = uni.getStorageSync('city');
			this.pass = uni.getStorageSync('pass')
			// #ifdef  MP-WEIXIN
			// this.weixin = true
			// #endif
		},
		data() {
			return {
				city: 1,
				page: 1,
				list: [],
				pid: 0,
				remark: '',
				codenum: 1,
				title: '',
				position: 0,
				isok: 0,
				bid: 0,
				pass: false,
				weixin: false
			}
		},
		onReachBottom() {
			this.getmore()
		},
		methods: {
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
				let city = this.city
				uni.request({
					url: that.javaserve + '/applets/jy/city/dynamics',
					method: 'POST',
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					data: {
						city: city,
						page: that.page,
						limit: 10,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					success: (res) => {
						that.list = res.data.data.data
						//#ifdef MP-BAIDU
						let header = res.data.data.header
						swan.setPageInfo({
							title: header.title,
							keywords: header.keywords,
							description: header.description,
							success: res => {
								console.log('setPageInfo success', res);
							},
							fail: err => {
								console.log('setPageInfo fail', err);
							}
						})
						//#endif
						uni.hideLoading()
					}
				})
			},
			getmore() {
				uni.showLoading({
					title: "加载中"
				})
				that.page = that.page + 1
				let city = uni.getStorageSync('city')
				uni.request({
					url: that.javaserve + '/applets/jy/city/dynamics',
					method: 'POST',
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					data: {
						city: city,
						page: that.page,
						limit: 10,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					success: (res) => {
						that.list = that.list.concat(res.data.data.data)
						uni.hideLoading()
					}
				})
			},
			show(id, txt, isok) {
				this.pid = id
				this.remark = txt
				this.position = 98
				this.title = '订阅实时动态'
				console.log(this.pid)
				this.isok = isok
				this.$refs.popup.show()
			},
			setiscode() {
				this.codenum = 0
			},
			go(id) {
				uni.redirectTo({
					url: "/pages/dynamicdetail/dynamicdetail?id=" + id
				})
			},
			nav(e) {
				console.log(e)
				this.pid = 0
				this.position = 103
				this.remark = '动态页+预约看房'
				this.title = e.title
				this.isok = e.isok
				this.$refs.popup.show()
			},
			async getPhoneNumber(e, id) {
				console.log(e)
				this.bid = id
				let that = this
				// #ifdef  MP-BAIDU
				if (e.detail.errMsg == 'getPhoneNumber:fail auth deny') {
					this.isok = 0
					that.show(that.bid, '动态页+订阅楼盘动态', 0)

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
								that.show(that.bid, '动态页+订阅楼盘动态', 1)
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
												that.show(that.bid, '动态页+订阅楼盘动态',
													1)
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
					this.isok = 1
				}
				// #endif
				// #ifdef  MP-WEIXIN
				if (e.detail.errMsg != 'getPhoneNumber:ok') {
					this.isok = 0
					that.show(that.bid, '动态页+订阅楼盘动态', 0)

				} else {
					this.pass = true
					uni.setStorageSync('pass', true)
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
											let openid = uni.getStorageSync(
												'openid')
											that.tel = tel
											that.show(that.bid, '动态页+订阅楼盘动态', 1)
										}
									})

								}
							})
						}
					});
					this.isok = 1
				}
				// #endif

			}
		}
	}
</script>

<style lang="less">
	page {
		background: #F5F5F5;
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
			width: 31.87rpx;
			height: 31.87rpx;
			margin-right: 11.95rpx;
			margin-bottom: -3.98rpx;
		}
	}

	.topbb {
		height: var(--status-bar-height);
		width: 100%;
	}

	.topimg {
		width: 100%;
		height: 199.2rpx;
	}

	.box {
		padding: 0 49.8rpx;
		padding-bottom: 119.52rpx;
		padding-top: 42rpx;

		.item {
			background: #FFFFFF;
			box-shadow: 0px 0px 18.92rpx 0.99rpx rgba(0, 0, 0, 0.04);
			border-radius: 15.93rpx;
			overflow: hidden;
			padding-bottom: 39.84rpx;
			margin-bottom: 49.8rpx;

			.top {
				position: relative;
				margin-bottom: 23.9rpx;

				image {
					width: 647.41rpx;
					height: 239.04rpx;
					border-radius: 15.93rpx 15.93rpx 0 0;
				}

				.zhao {
					width: 100%;
					height: 239.04rpx;
					position: absolute;
					top: 0;
					left: 0;
					background: linear-gradient(0deg, #000000);
					opacity: 0.4;
				}

				.topicon {
					position: absolute;
					width: 79.68rpx;
					height: 35.85rpx;
					background: linear-gradient(270deg, #FF7C48, #FF4234);
					border-radius: 15.93rpx 0px 15.93rpx 0px;
					text-align: center;
					line-height: 35.85rpx;
					color: #FFF2F2;
					font-size: 23.9rpx;
					left: 0;
					top: 0;
				}

				.name {
					color: #FFFFFF;
					font-size: 33.86rpx;
					font-weight: bold;
					position: absolute;
					left: 27.88rpx;
					bottom: 79.68rpx;
				}

				.msg {
					position: absolute;
					bottom: 27.88rpx;
					left: 27.88rpx;
					font-size: 25.89rpx;
					color: #FFFFFF;

					text {
						margin-right: 35.85rpx;
					}
				}
			}

			.bom {
				padding: 0 29.88rpx;

				button {
					padding: 0;
				}

				button::after {
					border: none;
				}

				.name {
					color: #17181A;
					font-size: 31.87rpx;
					font-weight: bold;
					margin-bottom: 9.96rpx;
				}

				.txt {
					color: #4B4C4D;
					font-size: 25.89rpx;
					line-height: 37.84rpx;

				}

				.time {
					margin-top: 15.93rpx;
					color: #969799;
					font-size: 23.9rpx;
					margin-bottom: 29.88rpx;
				}

				.btn {
					position: relative;
					color: #20C657;
					font-size: 29.88rpx;
					text-align: center;
					line-height: 71.71rpx;
					width: 498rpx;
					border-radius: 11.95rpx;
					border: 1rpx solid #20C657;
					left: 50%;
					margin-left: -249rpx;
					background-color: #FFFFFF;
				}
			}
		}
	}
</style>
