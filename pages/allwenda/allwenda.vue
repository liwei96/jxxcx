<template>
	<view class="allwenda">
		<!-- <view class="toptitle">
			 <view class="status_bar">
			  </view>
			<navigator :url="`/pageA/content/content?id=${project_id}`" class="nav_top" open-type="navigate">
				<image src="../../static/all-back1.png" mode=""></image>
				<text>楼盘问问</text>
			</navigator>
		</view> -->
		<view class="img_box">
			<image src="../../static/other/all_wen_top.jpg" mode=""></image>
		</view>
		<view class="wen_list">
			<view class="list_one" v-for="item in data" :key="item.id">
				<navigator :url="`../wendadetail/wendadetail?id=${item.id}`">
					<view class="tit">
						<text class="wen">问</text>
						{{item.question}}
					</view>
				</navigator>

				<view class="da" v-if="item.answer!==''">
					<navigator :url="`../wendadetail/wendadetail?id=${item.id}`">
						<view class="top">
							<image :src="item.people[0].image" mode=""></image>
							<view class="rig">
								<view class="name_box">
									{{item.people[0].name}}
									<text>专业解答</text>
								</view>
								<view class="pp">
									咨询师帮您在线解答
								</view>
							</view>
						</view>
						<view class="bottom">
							{{item.answer.substring(0,35)}}
							<text v-if="item.answer.length>=37">[全文]</text>
						</view>
					</navigator>
				</view>
				<view class="da_box" v-else>
					<button class="da" hover-class="none" open-type="getPhoneNumber"
						@getphonenumber="getPhoneNumber($event,item.id,1)" v-if="!pass&&!weixin">
						我来回答
					</button>
					<button class="da" @tap="goTiwen(item.id)" v-if="pass||weixin">我来回答</button>
				</view>


			</view>
			<!-- 家园房友 -->
			<!-- <view class="list_one_yun">
					<view class="tit">
						<text class="wen">问</text>
						 本项目厨房管道的排风原理和方式是什么？会不
						会出现油烟倒灌情况?
					</view>
					<view class="da">
						<view class="top">
							<image src="../../../static/content/ping_img.png" mode=""></image>
							<view class="rig">
								<view class="name_box">
									167****4567
								</view>
								<view class="pp">
									家园房友
								</view>
							</view>
						</view>
						<view class="bottom">
							厨房排气道采用等截面变压式排气道，并在进气口设置可调变压防火止回阀，在管道内部，气...
						</view>
					</view> -->
			<!-- 			</view> -->
			<!-- 写问答 -->
			<button open-type="getPhoneNumber" hover-class="none" @getphonenumber="getPhoneNumber($event,project_id,2)"
				v-if="!pass&&!weixin">
				<view class="white_wen">
					<image src="../../static/other/loudian.png" mode=""></image>
				</view>
			</button>
			<view class="white_wen" v-if="pass||weixin" @tap="goQuestion(project_id)">
				<image src="../../static/other/loudian.png" mode=""></image>
			</view>

			<bottom :remark="remark" :point="103" :title="'预约看房'" :pid="parseInt(project_id)" :telphone="telphone" ref="bottom">
			</bottom>
			<!-- 登录弹框 -->
			<wyb-popup ref="login" type="bottom" height="570" width="650" radius="0" :showCloseIcon="true"
				closeIconSize="32" @hide="setiscode">
				<login @login="logined"></login>
			</wyb-popup>
		</view>
	</view>
</template>

<script>
	import bottom from '../../components/mine/bottom.vue'
	import login from "@/components/login.vue";
	var that;
	export default {
		data() {
			return {
				data: [],
				project_id: '',
				remark: '',
				telphone: '',
				page: 1,
				hua: true,
				pass: false,
				weixin: false,
				typebtn: ''
			};
		},
		components: {
			bottom,
			login
		},

		onLoad(option) {
			if (option.id == 0 || option.id == undefined || option.id == null) {
				this.remark = "全部问答页+预约看房"
			} else {
				this.remark = "项目问答页+预约看房"
			}
			that = this;
			this.getdata(option.id);
			this.project_id = option.id;
			console.log(this.project_id)
			this.pass = uni.getStorageSync('pass')
			// #ifdef  MP-WEIXIN
			// this.weixin = true
			// #endif
		},
		onReachBottom() {
			if (this.hua == true) {
				this.getmore(this.project_id);
			}
		},
		methods: {
			logined() {
				this.pass = true;
				this.$refs.bottom.pass = true
				this.$refs.login.hide();
				if (this.typebtn == 1) {
					that.goTiwen(hui_id)
				} else if (this.typebtn == 2) {
					that.goQuestion(hui_id)
				}
			},
			goQuestion(id) { //去提问
				let token = uni.getStorageSync('token');
				if (token) {
					let url = '/pages/allwenda/allwenda?id=' + this.project_id;
					uni.setStorageSync('backurl', url)
					uni.navigateTo({
						url: "../tiwen/tiwen?id=" + id
					})
				} else {
					this.$refs.login.show();
					// let url = '/pages/allwenda/allwenda?id=' + this.project_id
					// uni.setStorageSync('backurl', url)
					// uni.navigateTo({
					// 	url: '/pages/login/login'
					// })
				}

			},
			goTiwen(id) { //去回复问题
				let token = uni.getStorageSync('token');
				if (token) {
					let url = '/pages/allwenda/allwenda?id=' + this.project_id;
					uni.setStorageSync('backurl', url)
					uni.navigateTo({
						url: "../wenhui/wenhui?id=" + id
					})
				} else {
					let url = '/pages/allwenda/allwenda?id=' + this.project_id
					uni.setStorageSync('backurl', url)
					uni.navigateTo({
						url: '/pages/login/login'
					})
				}

			},
			getPhoneNumber(e, hui_id, type) {
				let that = this
				// #ifdef  MP-BAIDU
				if (e.detail.errMsg == 'getPhoneNumber:fail auth deny') {
					let token = uni.getStorageSync('token')
					if (token) {
						if (type == 1) {
							that.goTiwen(hui_id)
						} else if (type == 2) {
							that.goQuestion(hui_id)
						}
					} else {
						this.typebtn = type
						this.$refs.login.show();
						// this.isok = 0
						// let url = '/pages/allwenda/allwenda?id=' + this.project_id
						// uni.setStorageSync('backurl', url)
						// uni.navigateTo({
						// 	url: '/pages/login/login'
						// })
					}
				} else {
					this.pass = true
					uni.setStorageSync('pass', true)
					let session = uni.getStorageSync('token')
					if (session) {
						if (type == 1) {
							that.goTiwen(hui_id)
						} else if (type == 2) {
							that.goQuestion(hui_id)
						}

					} else {
						swan.getLoginCode({
							success: res => {
								console.log(res.code);
								uni.request({
									url: 'https://java.edefang.net/applets/jy/session_key/get',
									method: 'get',
									data: {
										code: res.code
									},
									success: (res) => {
										console.log(res)
										uni.setStorageSync('openid', res.data.data.openid)
										uni.setStorageSync('session', res.data.data.session_key)
										uni.request({
											url: "https://java.edefang.net/applets/jy/decrypt",
											data: {
												ciphertext: e.detail.encryptedData,
												iv: e.detail.iv,
												sessionKey: res.data.data.session_key
											},
											method: "POST",
											header: {
												'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
											},
											success: (res) => {
												console.log(res)
												let tel = res.data.data.mobile
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
														bid: that.project_id,
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
														uni.setStorageSync('pass', true)
														uni.setStorageSync(
															"token",
															res.data
															.data);
														if (type == 1) {
															that.goTiwen(
																hui_id)
														} else if (type ==
															2) {
															that.goQuestion(
																hui_id)
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
					this.isok = 1
				}
				// #endif
				// #ifdef  MP-WEIXIN
				if (e.detail.errMsg != 'getPhoneNumber:ok') {
					let token = uni.getStorageSync('token')
					if (token) {
						if (type == 1) {
							that.goTiwen(hui_id)
						} else if (type == 2) {
							that.goQuestion(hui_id)
						}
					} else {
						this.isok = 0
						let url = '/pages/allwenda/allwenda?id=' + this.project_id
						uni.setStorageSync('backurl', url)
						uni.navigateTo({
							url: '/pages/login/login'
						})
					}
				} else {
					this.pass = true
					uni.setStorageSync('pass', true)
					let session = uni.getStorageSync('token')
					if (session) {
						if (type == 1) {
							that.goTiwen(hui_id)
						} else if (type == 2) {
							that.goQuestion(hui_id)
						}

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
														if (type == 1) {
															that.goTiwen(
																hui_id)
														} else if (type ==
															2) {
															that.goQuestion(
																hui_id)
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
					this.isok = 1
				}
				// #endif
			},
			getdata(id) {
				uni.showLoading({
					title: '加载中'
				});
				let token = uni.getStorageSync('token');
				let other = uni.getStorageSync('other');
				let page = that.page;
				let city_id = uni.getStorageSync('city');
				if (id&&id!=0) {
					uni.request({
						url: that.javaserve + "/applets/jy/questions",
						data: {
							page: page,
							limit: "10",
							id: id, //如果是全部问答project 传0
							other: other,
							token: token,
							other: uni.getStorageSync('other'),
							uuid: uni.getStorageSync('uuid')
						},
						method: "GET",
						success: (res) => {
							if (res.data.status == 200) {
								that.data = res.data.data.data;
								that.telphone = res.data.data.phone;
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
							}
						},
						fail: (error) => {
							console.log(error);
						}
					})
				}else{
					uni.request({
						url: that.javaserve + "/applets/jy/city/questions",
						data: {
							page: page,
							limit: "10",
							city: city_id, //如果是全部问答project 传0
							other: other,
							token: token,
							other: uni.getStorageSync('other'),
							uuid: uni.getStorageSync('uuid')
						},
						method: "GET",
						success: (res) => {
							if (res.data.status == 200) {
								that.data = res.data.data.data;
								that.telphone = res.data.data.phone;
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
							}
						},
						fail: (error) => {
							console.log(error);
						}
					})
				}
			},
			getmore(id) {
				let city_id = uni.getStorageSync('city');
				let token = uni.getStorageSync('token');
				let other = uni.getStorageSync('other');
				that.page = that.page + 1;
				if (id&&id!=0) {
					uni.request({
						url: that.javaserve + "/applets/jy/questions",
						data: {
							page: that.page,
							limit: "10",
							id: id, //如果是全部问答project 传0
							other: other,
							token: token,
							other: uni.getStorageSync('other'),
							uuid: uni.getStorageSync('uuid')
						},
						method: "GET",
						success: (res) => {
							if (res.data.status == 200) {
								that.data = that.data.concat(res.data.data.data);
								uni.hideLoading();
							}
						},
						fail: (error) => {
							console.log(error);
						}
					})
				}else{
					uni.request({
						url: that.javaserve + "/applets/jy/city/questions",
						data: {
							page: that.page,
							limit: "10",
							city: city_id, //如果是全部问答project 传0
							other: other,
							token: token,
							other: uni.getStorageSync('other'),
							uuid: uni.getStorageSync('uuid')
						},
						method: "GET",
						success: (res) => {
							if (res.data.status == 200) {
								that.data = that.data.concat(res.data.data.data);
								uni.hideLoading();
							}
						},
						fail: (error) => {
							console.log(error);
						}
					})
				}
				// uni.request({
				// 	url: that.javaserve + "/applets/jy/city/questions",
				// 	data: {
				// 		page: that.page,
				// 		limit: "10",
				// 		city: city_id, //如果是全部问答project 传0
				// 		other: other,
				// 		token: token,
				// 		other: uni.getStorageSync('other'),
				// 		uuid: uni.getStorageSync('uuid')
				// 	},
				// 	method: "GET",
				// 	success: (res) => {
				// 		if (res.data.status == 200) {
				// 			that.data = that.data.concat(res.data.data.data);
				// 			uni.hideLoading();
				// 		}
				// 	},
				// 	fail: (error) => {
				// 		console.log(error);
				// 	}
				// })
			}
			// showQuan(){
			// 	this.old_flag = false;
			// 	this.quan_false = true;
			// }
		},

	}
</script>

<style lang="less">
	page {
		background: #fff;
		padding-bottom: 30rpx;
	}

	button::after {
		border: none;
	}

	.allwenda {
		.img_box {
			image {
				width: 100%;
				height: 200rpx;
			}
		}

		.wen_list {
			padding-left: 30rpx;
			padding-right: 30rpx;
			box-sizing: border-box;
			width: 100%;
			background: #fff;
			padding-bottom: 110rpx;

			.list_one {
				.tit {
					.wen {
						width: 30rpx;
						height: 30rpx;
						background: #FF5454;
						border-radius: 4rpx;
						font-size: 20rpx;
						font-weight: 500;
						color: #FFFFFF;
						display: inline-block;
						text-align: center;
						line-height: 30rpx;
						margin-right: 16rpx;
						font-weight: 500;
						position: relative;
						top: -4rpx;
					}

					font-size: 32rpx;
					color: #2E3133;
					line-height: 45rpx;
					margin-bottom: 32rpx;
					margin-top: 54rpx;
					font-weight: bold;
				}

				.da {
					.top {
						width: 660rpx;
						height: 66rpx;
						padding-top: 30rpx;
						background: #F7F7F7;
						padding-left: 30rpx;
						border-radius: 24rpx 24rpx 0rpx 0rpx;
						display: flex;

						image {
							width: 64rpx;
							height: 64rpx;
							border-radius: 32rpx;
							float: left;
							margin-right: 20rpx;
						}

						.rig {

							.name_box {
								font-size: 32rpx;
								font-weight: bold;
								color: #2E3133;
								position: relative;
								top: -3rpx;

								text {
									width: 92rpx;
									height: 28rpx;
									background: #FFC654;
									border-radius: 4rpx;
									font-size: 20rpx;
									font-weight: 400;
									color: #FFFFFF;
									text-align: center;
									line-height: 28rpx;
									margin-left: 8rpx;
									position: relative;
									top: -4rpx;
								}
							}

							.pp {
								font-size: 24rpx;
								color: #7D7E80;
								line-height: 30rpx;
							}

						}
					}

					.bottom {
						font-size: 32rpx;
						color: #464a4c;
						line-height: 44rpx;
						background-color: #F7F7F7;
						padding-left: 30rpx;
						padding-right: 30rpx;
						padding-bottom: 28rpx;
						padding-top: 24rpx;
						border-radius: 0rpx 0rpx 24rpx 24rpx;

						text {
							color: #628BB9;
						}
					}
				}

				.da_box {
					width: 100%;
					height: 52rpx;

					.da {
						width: 156rpx;
						height: 60rpx;
						border: 1rpx solid #AFB0B3;
						border-radius: 30rpx;
						font-size: 26rpx;
						font-weight: 500;
						color: #6E7275;
						text-align: center;
						line-height: 60rpx;
						float: right;
						padding-left: 0rpx;
						padding-right: 0rpx;
					}
				}

			}

			.list_one_no {
				.tit {
					.wen {
						width: 30rpx;
						height: 30rpx;
						background: #FF5454;
						border-radius: 4rpx;
						font-size: 20rpx;
						font-weight: 500;
						color: #FFFFFF;
						display: inline-block;
						text-align: center;
						line-height: 30rpx;
						margin-right: 25rpx;
					}

					font-size: 30rpx;
					font-weight: bold;
					color: #323233;
					line-height: 45rpx;
					margin-bottom: 32rpx;
					margin-top: 32rpx;
				}

				.da_box {
					width: 100%;
					height: 52rpx;

					.da {
						width: 140rpx;
						height: 52rpx;
						border: 1rpx solid #AFB0B3;
						border-radius: 26rpx;
						font-size: 24rpx;
						font-weight: 500;
						color: #4B4C4D;
						text-align: center;
						line-height: 52rpx;
						float: right;
					}
				}

			}

			.list_one_yun {
				.tit {
					.wen {
						width: 30rpx;
						height: 30rpx;
						background: #FF5454;
						border-radius: 4rpx;
						font-size: 20rpx;
						font-weight: 500;
						color: #FFFFFF;
						display: inline-block;
						text-align: center;
						line-height: 30rpx;
						margin-right: 25rpx;
					}

					font-size: 30rpx;
					font-weight: bold;
					color: #323233;
					line-height: 45rpx;
					margin-bottom: 32rpx;
					margin-top: 32rpx;
				}

				.da {
					.top {
						width: 660rpx;
						height: 66rpx;
						padding-top: 30rpx;
						background: #F7F7F7;
						padding-left: 30rpx;
						border-radius: 24rpx 24rpx 0rpx 0rpx;

						image {
							width: 64rpx;
							height: 64rpx;
							border-radius: 32rpx;
							float: left;
							margin-right: 20rpx;
						}

						.rig {
							float: left;

							.name_box {
								font-size: 30rpx;
								font-weight: bold;
								color: #121212;
								line-height: 30rpx;
							}

							.pp {
								font-size: 24rpx;
								font-weight: 500;
								color: #7D7E80;
								line-height: 24rpx;
								margin-top: 14rpx;
							}

						}
					}

					.bottom {
						font-size: 28rpx;
						font-weight: 500;
						color: #4B4C4D;
						line-height: 42rpx;
						background-color: #F7F7F7;
						padding-left: 30rpx;
						padding-bottom: 30rpx;
						padding-top: 21rpx;
						border-radius: 0rpx 0rpx 24rpx 24rpx;
					}
				}
			}

			.white_wen {
				width: 100rpx;
				height: 100rpx;
				position: fixed;
				bottom: 160rpx;
				right: 30rpx;

				image {
					width: 100rpx;
					height: 100rpx;
				}
			}
		}
	}
</style>
