<template>
	<view class="dynamicdetail" v-if="issure">
		<!-- <view class="toptitle" @tap="goback">
			<view class="status_bar">
			</view>
			<image src="../../static/all-back.png" mode=""></image>
			<text>动态详情</text>
		</view> -->
		<view class="box_1">
			<view class="dynamic-tit">{{info.title}}</view>
			<text class="txt">{{info.content}}</text>
			<view class="time">{{info.time}}</view>
			<button open-type="getPhoneNumber" @tap="type = 0" @getphonenumber="getPhoneNumber($event,98)"
				v-if="!pass&&!weixin">
				<view class="btn">
					订阅最新动态
				</view>
			</button>
			<view class="btn" v-if="pass||weixin" @tap="show(build.id,'订阅实时动态','动态详情页+订阅楼盘动态',1,98)">
				订阅最新动态
			</view>
			<view class="build" @tap="gobuild(build.id)">
				<view class="left">
					<image :src="build.image" mode=""></image>
				</view>
				<view class="right">
					<view class="right-tit">
						<text class="name">{{build.name}}</text>
						<view class="status">{{build.state}}</view>
					</view>
					<view>
						<text class="big">{{build.price}}</text>
						<text class="small">元/m²</text>
					</view>
					<view class="build-msg">
						{{build.type}} &nbsp;|&nbsp; {{build.city}}-{{build.country}} &nbsp;|&nbsp; {{build.area}}m²
					</view>
					<view class="icons">
						<text class="zhuang" v-if="build.decorate">{{build.decorate}}</text>
						<text v-if="build.railway">{{build.railway}}</text>
						<text v-if="build.feature">{{build.feature}}</text>
					</view>
				</view>
			</view>
			<view class="staff">
				<view class="left">
					<image :src="staff.image" mode="widthFix"></image>
				</view>
				<view class="staffmsg">
					<text class="name">{{staff.name}}</text>
					<text class="rate">满意度{{staff.num}}分</text>
					<view class="stafftxt">
						为客户提供专业的购房建议
					</view>
				</view>
				<button open-type="getPhoneNumber" @tap="type = 1" @getphonenumber="getPhoneNumber($event,87)"
					v-if="!pass&&!weixin">
					<view class="staffbtn">
						免费咨询
					</view>
				</button>
				<view class="staffbtn" v-if="pass||weixin" @tap="show(build.id,'免费咨询','动态详情页+免费咨询',1,87)">
					免费咨询
				</view>
			</view>
		</view>
		<view class="line"></view>
		<view class="box_2">
			<view class="other">
				<view class="tit">本楼盘户型</view>
				<view class="other-item" v-for="item in other" :key="item.id" @tap="gohu(item.id)">
					<view class="left">
						<image :src="item.small" mode=""></image>
					</view>
					<view class="right">
						<view class="right-tit">
							<text class="name">{{item.title}}</text>
							<view class="status">
								{{item.state}}
							</view>
						</view>
						<view class="list">
							<text class="type">建面：</text>
							<text class="key">{{item.area}}m²</text>
						</view>
						<view class="list">
							<text class="type">类型：</text>
							<text class="key">{{item.type}}</text>
						</view>
						<view class="list">
							<text class="type">总价：</text>
							<text class="key">约</text>
							<text class="big">{{item.price}}</text>
							<text class="key">万/套</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		<bom-nav ref="bottom" :tel="tel" @show="nav" :projectid="bid"></bom-nav>
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
		onLoad(options) {
			this.id = options.id
			that = this
			this.getinfo()
			// #ifdef  MP-WEIXIN
			// this.weixin = true
			// #endif
		},
		components: {
			"bom-nav": bomnav,
			sign,
			wybPopup
		},
		data() {
			return {
				issure: false,
				id: 0,
				info: {},
				build: {},
				other: [],
				pid: 0,
				remark: '',
				codenum: 1,
				tel: '',
				title: '',
				position: 0,
				staff: {},
				isok: 0,
				type: 0,
				bid: 0,
				pass: false,
				weixin: false
			}
		},
		mounted() {
			this.pass = uni.getStorageSync('pass')
		},
		methods: {
			setpop() {
				this.$refs.popup.hide()
			},
			goback() {
				uni.navigateBack({
					delta: 1
				});
			},
			getinfo() {
				uni.showLoading({
					title: '加载中'
				});
				let other = uni.getStorageSync('other')
				let token = uni.getStorageSync('token')
				uni.request({
					url: that.javaserve + "/applets/jy/dynamic",
					method: "GET",
					data: {
						id: that.id,
						other: other,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					success: (res) => {
						console.log(res)
						that.build = res.data.data.building
						that.bid = res.data.data.building.id
						that.other = res.data.data.house_types
						that.info = res.data.data.dynamic
						that.staff = res.data.data.staff
						that.tel = res.data.data.phone
						uni.setStorageSync('city', res.data.data.city.id)
						uni.setStorageSync('cityname', res.data.data.city.name)
						// #ifdef MP-BAIDU
						let header = res.data.data.header;
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
						// #endif
						uni.hideLoading();
						that.issure = true
					}
				})
			},
			gobuild(id) {
				uni.redirectTo({
					url: "/pageA/content/content?id=" + id
				})
			},
			async getPhoneNumber(e, p) {
				console.log(e, this.isok)
				let title = ''
				let message = ''
				console.log(this.type)
				if (this.type == 1) {
					title = '免费咨询'
					message = '动态详情页+免费咨询'
				} else {
					title = '订阅实时动态'
					message = '动态详情页+订阅楼盘动态'
				}
				let that = this
				// #ifdef  MP-BAIDU
				if (e.detail.errMsg == 'getPhoneNumber:fail auth deny') {
					this.isok = 0
					that.show(that.build.id, title, message, 0, p)
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
								that.show(that.build.id, title, message, 1, p)
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
												that.show(that.build.id, title,
													message, 1, p)
												uni.request({
													url: "https://java.edefang.net/applets/jy/login",
													method: "POST",
													header: {
														"Content-Type": "application/x-www-form-urlencoded;charset=utf-8",
													},
													data: {
														phone: tel,
														openid: openid,
														bid: that.build.id,
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
															uni.setStorageSync('pass',true)
															that.$refs.bottom.pass = true
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
					that.show(that.build.id, title, message, 0, p)
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
											that.show(that.build.id, title,
												message, 1, p)

										}
									})

								}
							})
						}
					});
				}
				// #endif

			},
			show(id, title, txt, isok, position) {
				this.pid = id
				this.remark = txt
				// this.position = 98
				this.position = position
				this.title = title
				this.isok = isok
				this.$refs.popup.show()
			},
			setiscode() {
				this.codenum = 0
			},
			nav(e) {
				console.log(e)
				this.pid = this.bid
				this.position = 103
				this.remark = '动态详情页+预约看房'
				this.title = e.title
				this.isok = e.isok
				this.$refs.popup.show()
			},
			gohu(id) {
				uni.redirectTo({
					url: "/pages/hudetail/hudetail?id=" + id
				})
			}
		},
		watch: {
			projectid(val) {
				console.log(val)
			}
		}
	}
</script>

<style lang="less">
	page {
		background: #fff;
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

		text {
			width: 221rpx;
			font-size: 32rpx;
			font-weight: 500;
			color: #17181A;
		}
	}

	.box_1 {
		padding: 0 29.88rpx;
		// padding-top: 88rpx;

		.dynamic-tit {
			color: #17181A;
			font-size: 31.87rpx;
			font-weight: bold;
			margin-bottom: 31.87rpx;
			margin-top: 27.88rpx
		}

		button {
			padding: 0;
			margin-left: 0;
			border: 0;
		}

		button:after {
			border: 0;
		}

		.txt {
			color: #4B4C4D;
			font-size: 27.88rpx;
			line-height: 41.83rpx;
		}

		.time {
			margin-top: 23.9rpx;
			color: #969799;
			font-size: 23.9rpx;
			margin-bottom: 39.84rpx;
		}

		.btn {
			width: 100%;
			height: 79.68rpx;
			border-radius: 11.95rpx;
			text-align: center;
			line-height: 79.68rpx;
			background-color: #E9F5EE;
			color: #38916C;
			font-size: 29.88rpx;
			font-weight: bold;
		}

		.build {
			padding: 23.9rpx 29.88rpx;
			margin-top: 49.8rpx;
			display: flex;
			background: #FFFFFF;
			box-shadow: 0px 0px 18.92rpx 0.99rpx rgba(0, 0, 0, 0.03);
			border-radius: 23.9rpx;
			margin-bottom: 35.85rpx;

			.left {
				margin-right: 27.88rpx;

				image {
					width: 219.12rpx;
					height: 159.36rpx;
					border-radius: 11.95rpx;
				}
			}

			.right {
				flex: 1;
				position: relative;
				top: -3.98rpx;

				.right-tit {
					margin-bottom: 3.98rpx;

					.name {
						color: #303233;
						font-size: 29.88rpx;
						font-weight: 800;
					}

					.status {
						width: 67.72rpx;
						height: 35.85rpx;
						border-radius: 5.97rpx;
						text-align: center;
						line-height: 35.85rpx;
						background-color: #E6FAF0;
						color: #20C657;
						font-size: 21.91rpx;
						float: right;
					}
				}

				.big {
					color: #FF6040;
					font-size: 31.87rpx;
					font-weight: bold;
				}

				.small {
					color: #FF6040;
					font-size: 25.89rpx;
				}

				.build-msg {
					color: #7D7E80;
					font-size: 25.89rpx;
					margin: 3.98rpx 0;
				}

				.icons {
					text {
						padding: 5.97rpx 11.95rpx;
						color: #7D7E80;
						font-size: 23.9rpx;
						background-color: #F5F5F5;
						border-radius: 3.98rpx;
						margin-right: 11.95rpx;
					}

					.zhuang {
						color: #49ABF1;
						background-color: #F0F5F9;
					}
				}
			}
		}

		.staff {
			display: flex;
			align-items: center;
			margin-bottom: 39.84rpx;

			.left {
				width: 64rpx;
				height: 64rpx;
				border-radius: 50%;
				overflow: hidden;
				margin-right: 15.93rpx;
			}

			image {
				width: 63.74rpx;
			}

			button {
				padding: 0;
				margin-left: auto;
				margin-right: 0;
				border: 0;
			}

			button:after {
				border: 0;
			}

			.staffmsg {
				.name {
					color: #303233;
					font-size: 31.87rpx;
					font-weight: bold;
				}

				.rate {
					color: #FFFFFF;
					font-size: 19.92rpx;
					padding: 3.98rpx 7.96rpx;
					background-color: #FA941B;
					border-radius: 5.97rpx;
					margin-left: 4.98rpx;
				}

				.stafftxt {
					color: #969799;
					font-size: 23.9rpx;
					margin-top: 7.96rpx;
				}
			}

			.staffbtn {
				background: linear-gradient(270deg, #28C567, #81DB85);
				;
				border-radius: 27.88rpx;
				width: 147.41rpx;
				height: 55.77rpx;
				text-align: center;
				line-height: 55.77rpx;
				margin-left: auto;
				color: #FFFFFF;
				font-size: 23.9rpx;
			}
		}

		.other {
			padding-bottom: 99.6rpx;

			.tit {
				color: #17181A;
				font-size: 33.86rpx;
				font-weight: bold;
				margin-top: 35.85rpx;
				margin-bottom: 43.82rpx;
			}

			.other-item {
				display: flex;
				padding-bottom: 27.88rpx;
				border-bottom: 0.99rpx solid #F2F2F2;
				margin-bottom: 29.88rpx;

				.left {
					width: 219.12rpx;
					height: 159.36rpx;
					background-color: #F5F5F5;
					text-align: center;
					border-radius: 11.95rpx;
					margin-right: 25.89rpx;

					image {
						height: 159.36rpx;
						width: 119.52rpx;
					}
				}

				.right {
					flex: 1;
					position: relative;
					top: -7.96rpx;

					.right-tit {
						margin-bottom: 7.96rpx;

						.name {
							color: #323233;
							font-size: 31.87rpx;
							font-weight: bold;
						}

						.status {
							width: 67.72rpx;
							height: 35.85rpx;
							border-radius: 5.97rpx;
							text-align: center;
							line-height: 35.85rpx;
							background-color: #2FCB72;
							color: #FFFFFF;
							font-size: 21.91rpx;
							float: right;
						}
					}

					.list {
						.type {
							color: #7D7E80;
							font-size: 23.9rpx;
						}

						.key {
							color: #323233;
							font-size: 25.89rpx;
						}
					}

					.list:nth-of-type(4) {
						.key {
							color: #FF6040;
							font-size: 19.92rpx;
						}

						.big {
							color: #FF6040;
							font-size: 31.87rpx;
							font-weight: bold;
						}
					}
				}
			}
		}
	}

	.box_2 {
		padding: 0 29.88rpx;
		// // padding-top: 88rpx;
		margin-top: var(--status-bar-height);

		.dynamic-tit {
			color: #17181A;
			font-size: 31.87rpx;
			font-weight: bold;
			margin-bottom: 31.87rpx;
			margin-top: 27.88rpx
		}

		button {
			padding: 0;
			margin-left: 0;
			border: 0;
		}

		button:after {
			border: 0;
		}

		.txt {
			color: #4B4C4D;
			font-size: 27.88rpx;
			line-height: 41.83rpx;
		}

		.time {
			margin-top: 23.9rpx;
			color: #969799;
			font-size: 23.9rpx;
			margin-bottom: 39.84rpx;
		}

		.btn {
			width: 100%;
			height: 79.68rpx;
			border-radius: 11.95rpx;
			text-align: center;
			line-height: 79.68rpx;
			background-color: #F0F6FA;
			color: #40A2F4;
			font-size: 29.88rpx;
			font-weight: bold;
		}

		.build {
			padding: 23.9rpx 29.88rpx;
			margin-top: 49.8rpx;
			display: flex;
			background: #FFFFFF;
			box-shadow: 0px 0px 18.92rpx 0.99rpx rgba(0, 0, 0, 0.03);
			border-radius: 23.9rpx;
			margin-bottom: 35.85rpx;

			.left {
				margin-right: 27.88rpx;

				image {
					width: 219.12rpx;
					height: 159.36rpx;
					border-radius: 11.95rpx;
				}
			}

			.right {
				flex: 1;
				position: relative;
				top: -3.98rpx;

				.right-tit {
					margin-bottom: 3.98rpx;

					.name {
						color: #303233;
						font-size: 29.88rpx;
						font-weight: 800;
					}

					.status {
						width: 67.72rpx;
						height: 35.85rpx;
						border-radius: 5.97rpx;
						text-align: center;
						line-height: 35.85rpx;
						background-color: #E6FAF0;
						color: #20C657;
						font-size: 21.91rpx;
						float: right;
					}
				}

				.big {
					color: #FF6040;
					font-size: 31.87rpx;
					font-weight: bold;
				}

				.small {
					color: #FF6040;
					font-size: 25.89rpx;
				}

				.build-msg {
					color: #7D7E80;
					font-size: 25.89rpx;
					margin: 3.98rpx 0;
				}

				.icons {
					text {
						padding: 5.97rpx 11.95rpx;
						color: #7D7E80;
						font-size: 23.9rpx;
						background-color: #F5F5F5;
						border-radius: 5rpx;
						margin-right: 11.95rpx;
					}

					.zhuang {
						color: #49ABF1;
						background-color: #F0F5F9;
					}
				}
			}
		}

		.staff {
			display: flex;
			align-items: center;
			margin-bottom: 39.84rpx;

			.left {
				width: 64rpx;
				height: 64rpx;
				border-radius: 50%;
				overflow: hidden;
				margin-right: 15.93rpx;
			}

			image {
				width: 63.74rpx;
			}

			button {
				padding: 0;
				margin-left: auto;
				margin-right: 0;
				border: 0;
			}

			button:after {
				border: 0;
			}

			.staffmsg {
				.name {
					color: #303233;
					font-size: 31.87rpx;
					font-weight: bold;
				}

				.rate {
					color: #FFFFFF;
					font-size: 19.92rpx;
					padding: 3.98rpx 7.96rpx;
					background-color: #FA941B;
					border-radius: 5.97rpx;
					margin-left: 4.98rpx;
				}

				.stafftxt {
					color: #969799;
					font-size: 23.9rpx;
					margin-top: 7.96rpx;
				}
			}

			.staffbtn {
				background: linear-gradient(-45deg, #38A7EA, #63D5FF);
				border-radius: 27.88rpx;
				width: 147.41rpx;
				height: 55.77rpx;
				text-align: center;
				line-height: 55.77rpx;
				margin-left: auto;
				color: #FFFFFF;
				font-size: 23.9rpx;
			}
		}

		.other {
			padding-bottom: 99.6rpx;

			.tit {
				color: #17181A;
				font-size: 33.86rpx;
				font-weight: bold;
				margin-top: 35.85rpx;
				margin-bottom: 43.82rpx;
			}

			.other-item {
				display: flex;
				padding-bottom: 27.88rpx;
				border-bottom: 0.99rpx solid #F2F2F2;
				margin-bottom: 29.88rpx;

				.left {
					width: 219.12rpx;
					height: 159.36rpx;
					background-color: #F5F5F5;
					text-align: center;
					border-radius: 11.95rpx;
					margin-right: 25.89rpx;

					image {
						height: 159.36rpx;
						width: 119.52rpx;
					}
				}

				.right {
					flex: 1;
					position: relative;
					top: -7.96rpx;

					.right-tit {
						margin-bottom: 7.96rpx;

						.name {
							color: #323233;
							font-size: 31.87rpx;
							font-weight: bold;
						}

						.status {
							width: 67.72rpx;
							height: 35.85rpx;
							border-radius: 5.97rpx;
							text-align: center;
							line-height: 35.85rpx;
							background-color: #2FCB72;
							color: #FFFFFF;
							font-size: 21.91rpx;
							float: right;
						}
					}

					.list {
						.type {
							color: #7D7E80;
							font-size: 23.9rpx;
						}

						.key {
							color: #323233;
							font-size: 25.89rpx;
						}
					}

					.list:nth-of-type(4) {
						.key {
							color: #FF6040;
							font-size: 19.92rpx;
						}

						.big {
							color: #FF6040;
							font-size: 31.87rpx;
							font-weight: bold;
						}
					}
				}
			}
		}
	}

	.line {
		width: 100%;
		height: 19.92rpx;
		background-color: #F7F7F7;
	}
</style>
