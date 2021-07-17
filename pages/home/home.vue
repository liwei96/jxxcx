<template>
	<view class="home">
		<!-- <view class="toptitle">
			<text>个人中心</text>
		</view> -->
		<view class="top-nav">
			<view class="login">
				<image src="../../static/home/home-peo.png" mode=""></image>
				<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber" @tap="type = 4" v-if="!tel">
					<text>登录/注册</text>
				</button>
				<!-- <text v-if="weixin&&!tel" @click="gologin">登录/注册</text> -->
				<text v-if="tel">{{ tel }}</text>
			</view>
			<view class="navs">
				<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber" @tap="type = 1"
					v-if="!pass && !weixin">
					<view class="nav">
						<text class="num">{{ footnum }}</text>
						<view>
							<text class="msg">浏览足迹</text>
						</view>
					</view>
				</button>
				<view class="nav" v-if="pass || weixin" @tap="gofoot">
					<text class="num">{{ footnum }}</text>
					<view>
						<text class="msg">浏览足迹</text>
					</view>
				</view>
				<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber" @tap="type = 2"
					v-if="!pass && !weixin">
					<view class="nav">
						<text class="num">{{ collectnum }}</text>
						<view>
							<text class="msg">我的收藏</text>
						</view>
					</view>
				</button>
				<view class="nav" v-if="pass || weixin" @tap="gofork">
					<text class="num">{{ collectnum }}</text>
					<view>
						<text class="msg">我的收藏</text>
					</view>
				</view>
				<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber" @tap="type = 3"
					v-if="!pass && !weixin">
					<view class="nav">
						<text class="num">{{ cardnum }}</text>
						<view>
							<text class="msg">我的卡券</text>
						</view>
					</view>
				</button>
				<view class="nav" v-if="pass || weixin" @tap="gocards">
					<text class="num">{{ cardnum }}</text>
					<view>
						<text class="msg">我的卡券</text>
					</view>
				</view>
				<view class="nav" @tap="gotalk">
					<text class="num">1</text>
					<!-- <text class="num">{{ talknum }}</text> -->
					<!-- <text class="abo">1</text> -->
					<view>
						<text class="msg">我的联系</text>
					</view>
				</view>
			</view>
			<view class="bom">
				<view class="nav" @tap="gohelp">
					<image src="../../static/home/home-help.png" mode=""></image>
					<view>
						<text>帮我找房</text>
					</view>
				</view>
				<view class="nav" @tap="goyue">
					<image src="../../static/home/home-yue.png" mode=""></image>
					<view>
						<text>预约看房</text>
					</view>
				</view>
				<view class="nav" @tap="gomap">
					<image src="../../static/home/home-map.png" mode=""></image>
					<view>
						<text>地图找房</text>
					</view>
				</view>
				<view class="nav" @tap="gojia" v-if="false">
					<image src="../../static/home/home-join.png" mode=""></image>
					<view>
						<text>城市加盟</text>
					</view>
				</view>
			</view>
		</view>
		<view class="box">
			<view class="connav">
				<view class="nav" @tap="goabout">
					<image class="icon" src="../../static/home/home-about.png" mode=""></image>
					<text>关于家园</text>
					<image class="goicon" src="../../static/other/goicon.png" mode=""></image>
				</view>
				<view class="nav" @tap="show">
					<image class="icon" src="../../static/home/home-tel.png" mode=""></image>
					<text>联系我们</text>
					<image class="goicon" src="../../static/other/goicon.png" mode=""></image>
				</view>
				<view class="nav" @tap="gocommit">
					<image class="icon" src="../../static/home/home-msg.png" mode=""></image>
					<text>意见反馈</text>
					<image class="goicon" src="../../static/other/goicon.png" mode=""></image>
				</view>
			</view>
			<view class="bomnv">
				<view class="nav" @tap="goban">
					<image class="icon" src="../../static/home/home-ban.png" mode=""></image>
					<text>版权声明</text>
					<image class="goicon" src="../../static/other/goicon.png" mode=""></image>
				</view>
				<view class="nav" @tap="gomian">
					<image class="icon" src="../../static/home/home-mian.png" mode=""></image>
					<text>免责协议</text>
					<image class="goicon" src="../../static/other/goicon.png" mode=""></image>
				</view>
				<view class="nav" @tap="goyin">
					<image class="icon" src="../../static/home/home-yin.png" mode=""></image>
					<text>隐私政策</text>
					<image class="goicon" src="../../static/other/goicon.png" mode=""></image>
				</view>
				<view class="nav" @tap="gofu">
					<image class="icon" src="../../static/home/home-xie.png" mode=""></image>
					<text>服务协议</text>
					<image class="goicon" src="../../static/other/goicon.png" mode=""></image>
				</view>
			</view>
		</view>
		<view class="outbtn" v-if="tel" @tap="getout"> 退出登录 </view>
		<popup ref="popup" type="center" height="300" width="540" radius="12">
			<view class="popup-content">
				<view class="txt"> 拨打电话 </view>
				<view class="tel"> 400-718-6686 </view>
				<view class="btnbox">
					<view class="left" @tap="hide"> 取消 </view>
					<view class="right" @tap="call"> 确定 </view>
				</view>
			</view>
		</popup>
		<!-- <tabbar :type="3"></tabbar> -->
	</view>
</template>

<script>
	import wybPopup from "@/components/wyb-popup/wyb-popup.vue";
	import tabbar from "../../components/tabbar/tabbar.vue";
	var that;
	export default {
		components: {
			popup: wybPopup,
			tabbar,
		},
		data() {
			return {
				footnum: 0,
				collectnum: 0,
				cardnum: 0,
				talknum: 0,
				tel: "",
				pass: false,
				weixin: false,
				baidu: true,
				sid: 152
			};
		},
		onShow() {
			that = this;
			// this.getlist();
			// this.timer = setInterval(() => {
			// 	that.getlist();
			// }, 2000);
			this.pass = uni.getStorageSync("pass");
			this.getinfo();
			if (uni.getStorageSync("phone")) {
				let tel = uni.getStorageSync("phone");
				this.tel = tel.substr(0, 3) + "****" + tel.substr(7);
			}
			// #ifdef  MP-WEIXIN
			// this.weixin = true
			this.baidu = false;
			// #endif
			//#ifdef MP-BAIDU
			swan.setPageInfo({
				title: "家园新房-我的主页",
				keywords: "家园新房-我的主页",
				description: "家园新房-我的主页",
				success: (res) => {
					console.log("setPageInfo success", res);
				},
				fail: (err) => {
					console.log("setPageInfo fail", err);
				},
			});
			//#endif
		},
		methods: {
			getout() {
				uni.removeStorageSync("phone");
				uni.removeStorageSync("token");
				uni.removeStorageSync("pass");
				this.pass = false;
				this.tel = "";
			},
			getlist() {
				let uuid = uni.getStorageSync("uuid");
				let pp = {
					controller: "Staff",
					action: "lists",
					params: {
						uuid: uuid,
					},
				};
				try {
					this.$store.state.socket.send({
						data: JSON.stringify(pp),
					});
				} catch (e) {
					alert(e);
				}
			},
			gologin() {
				uni.navigateTo({
					url: "/pages/login/login",
				});
			},
			call() {
				uni.makePhoneCall({
					phoneNumber: "400-718-6686", //仅为示例
				});
			},
			getinfo() {
				let token = uni.getStorageSync("token");
				if (token) {
					uni.showLoading({
						title: "加载中",
					});
					uni.request({
						url:that.javaserve+'/applets/jy/mine',
						method:"GET",
						data:{
							token: token,
						},
						success: (res) => {
							console.log(res,'res')
							that.footnum = res.data.data.my_foot_count;
							that.collectnum = res.data.data.collect_count;
							uni.hideLoading()
						}
					})
					that.cardnum = 1;
				}
			},
			show() {
				this.$refs.popup.show();
			},
			hide() {
				this.$refs.popup.hide();
			},
			gofoot() {
				// #ifdef  MP-WEIXIN
				let token = uni.getStorageSync("token");
				if (!token) {
					let url = "/pages/footprint/footprint";
					uni.setStorageSync("backurl", url);
					uni.navigateTo({
						url: "/pages/login/login",
					});
					return;
				}
				// #endif
				uni.navigateTo({
					url: "/pages/footprint/footprint",
				});
			},
			gofork() {
				// #ifdef  MP-WEIXIN
				let token = uni.getStorageSync("token");
				if (!token) {
					let url = "/pages/collect/collect";
					uni.setStorageSync("backurl", url);
					uni.navigateTo({
						url: "/pages/login/login",
					});
					return;
				}
				// #endif
				uni.navigateTo({
					url: "/pages/collect/collect",
				});
			},
			gocards() {
				// #ifdef  MP-WEIXIN
				let token = uni.getStorageSync("token");
				if (!token) {
					let url = "/pages/cards/cards";
					uni.setStorageSync("backurl", url);
					uni.navigateTo({
						url: "/pages/login/login",
					});
					return;
				}
				// #endif
				uni.navigateTo({
					url: "/pages/cards/cards",
				});
			},
			gotalk() {
				// uni.navigateTo({
				// 	url: "/pages/message/message",
				// });
				let id = this.sid
				uni.navigateTo({
					url: '/pages/talk/talk?id=' + id
				})
			},
			register(pro, type) {
				let uuid = uni.getStorageSync('uuid')
				let city = uni.getStorageSync('city')
				let ip = uni.getStorageSync('ip')
				let arr = getCurrentPages()
				let url = arr[arr.length - 1].route
				let host = this.host
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
			gohelp() {
				uni.navigateTo({
					url: "/pages/help/help",
				});
			},
			goyue() {
				uni.navigateTo({
					url: "/pages/forward/forward",
				});
			},
			gomap() {
				uni.navigateTo({
					url: "/pages/map/map",
				});
			},
			gojia() {
				uni.navigateTo({
					url: "/pages/alliance/alliance",
				});
			},
			goabout() {
				uni.navigateTo({
					url: "/pages/about/about",
				});
			},
			gocommit() {
				uni.navigateTo({
					url: "/pages/feedback/feedback",
				});
			},
			goban() {
				uni.navigateTo({
					url: "/pages/statement/statement",
				});
			},
			gomian() {
				uni.navigateTo({
					url: "/pages/protocol/protocol",
				});
			},
			goyin() {
				uni.navigateTo({
					url: "/pages/privacy/privacy",
				});
			},
			gofu() {
				uni.navigateTo({
					url: "/pages/serve/serve",
				});
			},
			getPhoneNumber(e) {
				let that = this;
				console.log(e);
				// #ifdef  MP-BAIDU
				if (e.detail.errMsg == "getPhoneNumber:fail auth deny") {
					let url = "";
					switch (that.type) {
						case 1:
							url = "/pages/footprint/footprint";
							uni.setStorageSync("backurl", url);
							break;
						case 2:
							url = "/pages/collect/collect";
							uni.setStorageSync("backurl", url);
							break;
						case 3:
							url = "/pages/cards/cards";
							uni.setStorageSync("backurl", url);
							break;
						case 4:
							url = "/pages/home/home";
							uni.setStorageSync("backurl", url);
							break;
					}
					uni.navigateTo({
						url: "/pages/login/login",
					});
				} else {
					uni.setStorageSync("pass", true);
					that.pass = true;
					console.log(that.pass);
					let session = uni.getStorageSync("session");
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
								uni.setStorageSync("phone", tel);
								let openid = uni.getStorageSync("openid");
								that.tel = tel.substr(0, 3) + "****" + tel.substr(7);
								let token = uni.getStorageSync("token");
								if (token) {
									switch (that.type) {
										case 1:
											uni.navigateTo({
												url: "/pages/footprint/footprint",
											});
											break;
										case 2:
											uni.navigateTo({
												url: "/pages/collect/collect",
											});
											break;
										case 3:
											uni.navigateTo({
												url: "/pages/cards/cards",
											});
											break;
									}
								} else {
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
											switch (that.type) {
												case 1:
													uni.navigateTo({
														url: "/pages/footprint/footprint",
													});
													break;
												case 2:
													uni.navigateTo({
														url: "/pages/collect/collect",
													});
													break;
												case 3:
													uni.navigateTo({
														url: "/pages/cards/cards",
													});
													break;
											}
										},
									});
								}
							},
						});
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
												uni.setStorageSync("phone", tel);
												let openid = uni.getStorageSync(
													"openid");
												that.tel = tel.substr(0, 3) + "****" +
													tel.substr(7);
												let token = uni.getStorageSync(
												"token");
												if (token) {
													switch (that.type) {
														case 1:
															uni.navigateTo({
																url: "/pages/footprint/footprint",
															});
															break;
														case 2:
															uni.navigateTo({
																url: "/pages/collect/collect",
															});
															break;
														case 3:
															uni.navigateTo({
																url: "/pages/cards/cards",
															});
															break;
													}
												} else {
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
															switch (that
																.type) {
																case 1:
																	uni.navigateTo({
																		url: "/pages/footprint/footprint",
																	});
																	break;
																case 2:
																	uni.navigateTo({
																		url: "/pages/collect/collect",
																	});
																	break;
																case 3:
																	uni.navigateTo({
																		url: "/pages/cards/cards",
																	});
																	break;
															}
														},
													});
												}
											},
										});
									},
								});
							},
						});
					}
				}
				// #endif
				// #ifdef  MP-WEIXIN
				console.log(e);
				if (e.detail.errMsg == "getPhoneNumber:ok") {
					uni.login({
						provider: "weixin",
						success: function(res) {
							console.log(res);
							uni.request({
								url: "https://ll.edefang.net/api/weichat/jscode2session",
								method: "get",
								data: {
									code: res.code,
									other: uni.getStorageSync("other"),
									uuid: uni.getStorageSync("uuid"),
								},
								success: (res) => {
									console.log(res);
									uni.setStorageSync("openid", res.data.data.openid);
									uni.setStorageSync("session", res.data.data.session_key);
									uni.request({
										url: "https://ll.edefang.net/api/weichat/decryptData",
										data: {
											data: e.detail.encryptedData,
											iv: e.detail.iv,
											sessionKey: res.data.data.session_key,
											other: uni.getStorageSync("other"),
											uuid: uni.getStorageSync("uuid"),
										},
										success: (res) => {
											let data = JSON.parse(res.data.message);
											let tel = data.purePhoneNumber;
											uni.setStorageSync("phone", tel);
											let openid = uni.getStorageSync("openid");
											that.tel = tel.substr(0, 3) + "****" + tel
												.substr(7);
											let token = uni.getStorageSync("token");
											if (token) {
												switch (that.type) {
													case 1:
														uni.navigateTo({
															url: "/pages/footprint/footprint",
														});
														break;
													case 2:
														uni.navigateTo({
															url: "/pages/collect/collect",
														});
														break;
													case 3:
														uni.navigateTo({
															url: "/pages/cards/cards",
														});
														break;
												}
											} else {
												uni.request({
													url: "https://api.edefang.net/applets/login",
													method: "GET",
													data: {
														phone: tel,
														openid: openid,
														other: uni
															.getStorageSync(
																"other"),
														uuid: uni
															.getStorageSync(
																"uuid"),
													},
													success: (res) => {
														console.log(res);
														uni.setStorageSync(
															"token",
															res.data
															.token);
														switch (that
														.type) {
															case 1:
																uni.navigateTo({
																	url: "/pages/footprint/footprint",
																});
																break;
															case 2:
																uni.navigateTo({
																	url: "/pages/collect/collect",
																});
																break;
															case 3:
																uni.navigateTo({
																	url: "/pages/cards/cards",
																});
																break;
														}
													},
												});
											}
										},
									});
								},
							});
						},
					});
				} else {
					let url = "";
					switch (that.type) {
						case 1:
							url = "/pages/footprint/footprint";
							uni.setStorageSync("backurl", url);
							break;
						case 2:
							url = "/pages/collect/collect";
							uni.setStorageSync("backurl", url);
							break;
						case 3:
							url = "/pages/cards/cards";
							uni.setStorageSync("backurl", url);
							break;
						case 4:
							url = "/pages/home/home";
							uni.setStorageSync("backurl", url);
							break;
					}
					uni.navigateTo({
						url: "/pages/login/login",
					});
				}
				// #endif
			},
		},
		mounted() {
			let that = this;
			this.register(0)
			this.$store.state.socket.onMessage(function(res) {
				if (res.data.indexOf('{') === -1) {
					return
				}
				let data = JSON.parse(res.data);
				console.log(data);
				if (data.action == 302) {
					that.sid = data.sid
				}
				// if (data.action == 105) {
				// 	that.talknum = data.data.length;
				// }
			});
		},
		onHide() {
			clearInterval(this.timer);
		},
		beforeDestroy() {
			clearInterval(this.timer);
		},
	};
</script>

<style lang="less">
	.home {
		background-color: #f5f5f5;
		// min-height: 100vh;
		padding-bottom: 68rpx;
	}

	.toptitle {
		color: #17181a;
		font-size: 29.88rpx;
		padding: 0 29.88rpx;
		padding-top: 39.84rpx;
		line-height: 87.64rpx;
		background-color: #35ace7;
	}

	button {
		padding: 0;
		margin: 0;
		background: rgba(0, 0, 0, 0);
		background-color: rgba(0, 0, 0, 0);
		line-height: 1.3;
	}

	button::after {
		border: none;
	}

	.top-nav {
		padding: 0 29.88rpx;
		background: url(../../static/home/home-top.png);
		background-size: 100%;
		height: 334.66rpx;
		position: relative;

		.login {
			display: flex;
			align-items: center;

			image {
				width: 99.6rpx;
				height: 99.6rpx;
				margin-right: 29.88rpx;
			}

			button {
				margin-left: 0;
				padding: 0;
				background: rgba(0, 0, 0, 0);
			}

			button:after {
				border: 0;
			}

			text {
				color: #126d4c;
				font-weight: bold;
				font-size: 39.84rpx;
			}
		}

		.navs {
			display: flex;
			justify-content: space-around;
			margin-top: 31.87rpx;

			.nav {
				position: relative;
				text-align: center;

				.num {
					color: #126d4c;
					font-size: 35.85rpx;
					font-weight: bold;
				}

				.msg {
					color: #126d4c;
					font-size: 23.9rpx;
					margin-top: 11.95rpx;
				}

				.abo {
					position: absolute;
					display: block;
					width: 25.89rpx;
					height: 25.89rpx;
					border-radius: 50%;
					text-align: center;
					line-height: 25.89rpx;
					top: -11.95rpx;
					right: 0;
					background-color: #bee6ff;
					color: #126d4c;
					font-size: 19.92rpx;
				}
			}

			.nav:nth-of-type(4) {
				line-height: 1.4;
			}
		}

		.bom {
			width: 100%;
			height: 175.29rpx;
			border-radius: 23.9rpx;
			box-shadow: 0px 0px 33.86rpx 5.97rpx rgba(6, 0, 1, 0.03);
			display: flex;
			align-items: center;
			justify-content: space-around;
			background-color: #fff;
			margin-top: 27.88rpx;

			.nav {
				text-align: center;

				image {
					width: 63.74rpx;
					height: 63.74rpx;
				}

				text {
					color: #101214;
					font-size: 25.89rpx;
				}
			}
		}
	}

	.box {
		padding: 0 29.88rpx;
		margin-top: 123.55rpx;

		.connav {
			height: 298.8rpx;
			border-radius: 23.9rpx;
			background-color: #fff;
			margin-bottom: 29.88rpx;

			.nav {
				padding: 0 29.88rpx;
				line-height: 99.6rpx;
				display: flex;
				align-items: center;

				.icon {
					width: 35.85rpx;
					height: 35.85rpx;
					margin-right: 19.92rpx;
				}

				.goicon {
					margin-left: auto;
					width: 24rpx;
					height: 24rpx;
				}

				text {
					color: #101214;
					font-size: 27.88rpx;
				}
			}
		}

		.bomnv {
			height: 398.4rpx;
			border-radius: 23.9rpx;
			background-color: #fff;
			margin-bottom: 29.88rpx;

			.nav {
				padding: 0 29.88rpx;
				line-height: 99.6rpx;
				display: flex;
				align-items: center;

				.icon {
					width: 35.85rpx;
					height: 35.85rpx;
					margin-right: 19.92rpx;
				}

				.goicon {
					margin-left: auto;
					width: 24rpx;
					height: 24rpx;
				}

				text {
					color: #101214;
					font-size: 27.88rpx;
				}
			}
		}
	}

	.outbtn {
		margin: 0 30rpx;
		border-radius: 12rpx;
		background: #2ac66d;
		color: #ffffff;
		font-size: 30rpx;
		text-align: center;
		line-height: 80rpx;
		margin-top: 80rpx;
	}

	.popup-content {
		.txt {
			color: #666666;
			font-size: 32rpx;
			text-align: center;
			padding-top: 32rpx;
			margin-bottom: 30rpx;
		}

		.tel {
			color: #333333;
			font-size: 36rpx;
			font-weight: bold;
			margin-bottom: 46rpx;
			text-align: center;
		}

		.btnbox {
			display: flex;
			height: 100rpx;
			border-top: 1rpx solid #f7f7f7;

			view {
				width: 48%;
				line-height: 100rpx;
				text-align: center;
			}

			.left {
				color: #999999;
				font-size: 32rpx;
			}

			.right {
				color: #3eacf0;
				font-size: 32rpx;
			}
		}
	}
</style>
