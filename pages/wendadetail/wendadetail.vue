<template>
	<view class="wendadetail" v-if="issure">
		<!-- <view class="toptitle">
			<view class="status_bar">
			</view>
			<navigator class="nav_top" open-type="navigateBack" delta="1">
				<image src="../../static/all-back.png" mode=""></image>
				<text>问答详情</text>
			</navigator>
		</view> -->
		<view class="wen_top">
			<view class="tit">
				<text class="wen">问</text>
				{{data.question}}
			</view>

			<view class="pro_one" @tap="gopro(building.id)">
				<image :src="building.image" mode=""></image>
				<view class="right_pro">
					<view class="pro_name">{{building.name}}<text class="status">{{building.state}}</text></view>
					<view class="price">{{building.price}}<text>元/m²</text></view>
					<view class="type">
						{{building.type}}<text>|</text>{{building.city}}-{{building.country}}<text>|</text>{{building.area}}m²
					</view>
					<view class="tese">
						<text class="zhuang">{{building.decorate}}</text>
						<text class="other">{{building.type}}</text>
						<text class="other" v-if="building.railway">{{building.railway}}</text>
						<text class="other" v-for="item in building.feature">{{item}}</text>
					</view>
				</view>
			</view>
			<!-- 没回复-->
			<button class="huifu_btn" v-if="data.answer=='' && !pass&&!weixin" open-type="getPhoneNumber"
				hover-class="none"
				@getphonenumber="getPhoneNumber($event,data.bid,'项目问答详情页+免费咨询',104,'免费咨询',2,data.id)">
				我来回答
			</button>
			<view class="huifu_btn" v-if="data.answer=='' && pass||weixin" @tap="gowenda(data.id)">
				我来回答
			</view>
			<!-- 免费咨询 -->
			<view class="da" v-show="data.answer!==''">
				<view class="top">
					<view class="left_box">
						<image :src="staff.image" mode=""></image>
						<view class="rig">
							<view class="name_box">
								{{staff.name}}
								<text>专业解答</text>
							</view>
							<view class="pp">
								最近咨询<text>{{staff.num}}人</text>
							</view>
						</view>
					</view>
					<button class="right_btn" v-if="!pass&&!weixin" open-type="getPhoneNumber" hover-class="none"
						@getphonenumber="getPhoneNumber($event,data.bid,'项目问答详情页+免费咨询',104,'免费咨询')">
						免费咨询
					</button>
					<view class="right_btn" v-if="pass||weixin" @tap="baoMing(data.bid,'项目问答详情页+免费咨询',104,'免费咨询',1)">
						免费咨询
					</view>
				</view>
				<!-- 家园房友-->
				<!-- <view class="top">
					<view class="left_box">
						<image src="../../static/content/ping_img.png" mode=""></image>
						<view class="rig">
							<view class="name_box">
							138****4854
							</view>
							<view class="pp">
								家园房友
							</view>
						</view>
					</view>
				</view> -->
				<view class="bottom">
					{{data.answer}}
				</view>

				<view class="time_box">
					<view class="time">
						{{data.time}}
					</view>
					<button open-type="getPhoneNumber" v-if="data.my_like==1 && !pass&&!weixin" hover-class="none"
						@getphonenumber="getPhoneNumber($event,data.bid,'项目问答详情页+点赞',104,'点赞',1,data.id)">
						<view class="zan">
							<image src="../../static/content/zan.png" mode=""></image>
							有用({{data.like_count}})
						</view>
					</button>
					<view class="zan" v-if="data.my_like==1 && (pass||weixin)" @tap="wenDian(data.id)">
						<image src="../../static/content/zan.png" mode=""></image>
						有用({{data.like_count}})
					</view>

					<button open-type="getPhoneNumber" v-if="data.my_like==0 &&!pass&&!weixin" hover-class="none"
						@getphonenumber="getPhoneNumber($event,data.bid,'项目问答详情页+点赞',104,'点赞',1,data.id)">
						<view class="nozan">
							<image src="../../static/content/no_zan.png" mode=""></image>
							有用({{data.like_count}})
						</view>
					</button>
					<view class="nozan" v-if="data.my_like==0 &&(pass||weixin)" @tap="wenDian(data.id)">
						<image src="../../static/content/no_zan.png" mode=""></image>
						有用({{data.like_count}})
					</view>
				</view>
			</view>
		</view>
		<view class="hui_bg" v-if="relevant.length"></view>
		<!-- 	相关楼盘问答 -->
		<view class="about_wen" v-if="relevant.length">
			<view class="title">
				相关楼盘问答
			</view>
			<view class="wen_tit" v-for="(item,key) in relevant" :key="item.id" v-if="key<3">
				<navigator :url="`../wendadetail/wendadetail?id=${item.id}`">
					<text class="wen">问</text>
					{{item.question}}
				</navigator>
			</view>
			<view class="btn" @tap="showAllwen">
				查看{{cityname}}全部楼盘问答
			</view>
		</view>
		<view class="hui_bg"></view>
		<!-- 猜你喜欢 -->
		<view class="see_box">
			<view class="tit">
				猜你喜欢
			</view>
			<twosee :recommends="recommends"></twosee>
		</view>
		<bottom :remark="'项目问答详情页+预约看房'" :point="103" :title="'预约看房'" :pid="pid" :telphone="telphone" ref="bottom"></bottom>
		<wyb-popup ref="popup" type="center" height="750" width="650" radius="12" :showCloseIcon="true"
			@hide="setiscode">
			<sign :type="codenum" @closethis="setpop" :title="title_e" :pid="pid_d" :remark="remark_k"
				:position="position_n" :isok="isok"></sign>
		</wyb-popup>
		<!-- 登录弹框 -->
		<wyb-popup ref="login" type="bottom" height="570" width="650" radius="0" :showCloseIcon="true"
			closeIconSize="32" @hide="setiscode">
			<login @login="logined" :pid="building.id"></login>
		</wyb-popup>
		<mytoast ref="msg" :txt="msg"></mytoast>
	</view>
</template>

<script>
	import bottom from "../../components/mine/bottom.vue";
	import twosee from '../../components/pros.vue';
	import wybPopup from '@/components/wyb-popup/wyb-popup.vue'
	import sign from '@/components/sign.vue'
	import mytoast from "@/components/mytoast/mytoast.vue"
	import login from "@/components/login.vue";
	export default {
		data() {
			return {
				title: '猜你喜欢',
				building: {},
				data: [],
				recommends: [],
				relevant: [],
				staff: {},

				codenum: 1,
				title_e: '',
				pid_d: 0,
				remark_k: '',
				position_n: 0,
				telphone: '',
				isok: 0,
				msg: '',
				pass: false,
				weixin: false,
				typebtn: '',
				cityname: '',
				wen_id: 0,
				issure: false,
				pid: ''
			};
		},
		components: {
			bottom,
			twosee,
			login,
			wybPopup,
			sign,
			mytoast
		},
		onLoad(option) {
			// #ifdef  MP-WEIXIN
			// this.weixin = true
			// #endif
			console.log(option);
			this.getinfo(option.id)
			this.wen_id = option.id
			this.cityname = uni.getStorageSync('cityname')
			this.pass = uni.getStorageSync('pass')
		},
		methods: {
			logined() {
				this.pass = true;
				this.$refs.bottom.pass = true
				this.$refs.login.hide();
				if (this.typebtn == 1) { //点赞
					this.wenDian(this.wen_id)
				} else if (this.typebtn == 2) { //跳回答
					this.gowenda(this.wen_id)
				}
			},
			gopro(id) {
				uni.navigateTo({
					url: "/pageA/content/content?id=" + id
				})
			},
			setpop() {
				this.$refs.popup.hide()
			},
			//没有回答的，跳问答回答页
			gowenda(id) {
				let token = uni.getStorageSync("token")
				if (token) {
					let url = '/pages/wendadetail/wendadetail?id=' + this.data.id;
					uni.setStorageSync('backurl', url)
					uni.navigateTo({
						url: "/pages/wenhui/wenhui?id=" + id
					})
				} else {
					let url = "/pages/wendadetail/wendadetail?id=" + this.data.bid;
					uni.setStorageSync("backurl", url)
					uni.navigateTo({
						url: "/pages/login/login"
					})
				}

			},
			wenDian(id) {
				uni.request({
					url: this.javaserve + "/applets/jy/question/like",
					data: {
						id: id,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid'),
						token: uni.getStorageSync('token')
					},
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					method: "POST",
					success: (res) => {
						if (res.data.status == 200) {
							this.$refs.msg.show();
							this.msg = '点赞成功';
							this.getinfo(this.data.id)
						} else {
							this.$refs.msg.show();
							this.msg = res.data.msg || res.data.message;
							console.log(res)
						}
					}
				})
			},
			async getPhoneNumber(e, pid, remark, point, title, type, wen_id) {
				let that = this
				// #ifdef  MP-BAIDU
				if (e.detail.errMsg == 'getPhoneNumber:fail auth deny') {
					if (type) {
						let token = uni.getStorageSync("token");
						if (token) {
							if (type == 1) { //点赞
								this.wenDian(wen_id)
							} else if (type == 2) { //跳回答
								this.gowenda(wen_id)
							}
						} else {
							this.typebtn = type
							this.$refs.login.show();
							// let url = "/pages/wendadetail/wendadetail?id=" + pid;
							// uni.setStorageSync("backurl", url)
							// uni.navigateTo({
							// 	url: "/pages/login/login"
							// })
						}

					} else {
						this.isok = 0
						that.baoMing(pid, remark, point, title, 0)
					}
				} else {
					this.pass = true
					uni.setStorageSync('pass', true)
					if (type) {
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
											if (type == 1) { //点赞
												that.wenDian(wen_id)
											} else if (type == 2) { //跳回答
												that.gowenda(wen_id)
											}
										}
									})
								}
							})
						} else {
							console.log(session, "没保存session")
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
													sessionKey: res.data.data
														.session_key,
												},
												method: "POST",
												header: {
													"Content-Type": "application/x-www-form-urlencoded;charset=utf-8",
												},
												success: (res) => {
													console.log(res);
													let tel = res.data.data.mobile;
													uni.setStorageSync('phone',
														tel)
													let openid = uni
														.getStorageSync('openid')
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
																	"uuid"
																),
															city: uni
																.getStorageSync(
																	"city"
																),
														},
														success: (res) => {
															console
																.log(
																	res
																);
															uni.setStorageSync(
																"token",
																res
																.data
																.data
															);
															if (type ==
																1
															) { //点赞
																that.wenDian(
																	wen_id
																)
															} else if (
																type ==
																2
															) { //跳没回答的问答
																that.gowenda(
																	wen_id
																)
															}

														}
													})
													// that.$refs.sign.tel = tel
													// that.baoMing(pid,remark,point,title)
												}
											})

										}
									})
								}
							});
						}
					} else {
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
									that.tel = tel;
									that.baoMing(pid, remark, point, title, 1)
								}
							})
						} else {
							console.log(session, "没保存session")
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
													sessionKey: res.data.data
														.session_key,
												},
												method: "POST",
												header: {
													"Content-Type": "application/x-www-form-urlencoded;charset=utf-8",
												},
												success: (res) => {
													console.log(res);
													let tel = res.data.data.mobile;
													uni.setStorageSync('phone',
														tel)
													let openid = uni
														.getStorageSync('openid')
													// that.$refs.sign.tel = tel
													that.baoMing(pid, remark,
														point, title, 1)
												}
											})

										}
									})
								}
							});
						}
						this.isok = 1

					}
				}
				// #endif
				// #ifdef  MP-WEIXIN
				if (e.detail.errMsg != 'getPhoneNumber:ok') {
					if (type) {
						let token = uni.getStorageSync("token");
						if (token) {
							if (type == 1) { //点赞
								this.wenDian(wen_id)
							} else if (type == 2) { //跳回答
								this.gowenda(wen_id)
							}
						} else {
							let url = "/pages/wendadetail/wendadetail?id=" + pid;
							uni.setStorageSync("backurl", url)
							uni.navigateTo({
								url: "/pages/login/login"
							})
						}

					} else {
						this.isok = 0
						that.baoMing(pid, remark, point, title, 0)
					}
				} else {
					this.pass = true
					uni.setStorageSync('pass', true)
					if (type) {
						uni.login({
							provider: 'weixin',
							success: function(res) {
								// console.log(res.code);
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
										uni.setStorageSync('session', res.data.data
											.session_key)
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
												let tel = res.data.mobile
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
														console.log(
															res)
														uni.setStorageSync(
															'token',
															res
															.data
															.token)
														if (type ==
															1) { //点赞
															that.wenDian(
																wen_id
															)
														} else if (
															type == 2
														) { //跳没回答的问答
															that.gowenda(
																wen_id
															)
														}

													}
												})
												// that.$refs.sign.tel = tel
												// that.baoMing(pid,remark,point,title)
											}
										})

									}
								})
							}
						});
					} else {
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
										uni.setStorageSync('openid', res.data.openid)
										uni.setStorageSync('session', res.data.session_key)
										uni.request({
											url: "https://ll.edefang.net/api/weichat/decryptData",
											data: {
												data: e.detail.encryptedData,
												iv: e.detail.iv,
												session_key: res.data.session_key,
												other: uni.getStorageSync('other'),
												uuid: uni.getStorageSync('uuid')
											},
											success: (res) => {
												console.log(res)
												let data = JSON.parse(res.data
													.message)
												let tel = data.purePhoneNumber
												uni.setStorageSync('phone', tel)
												let openid = uni.getStorageSync(
													'openid')
												that.$refs.sign.tel = tel
												that.baoMing(pid, remark, point,
													title, 1)
											}
										})

									}
								})
							}
						});
						this.isok = 1
					}
				}
				// #endif
			},
			baoMing(pid, remark, point, title, n) {
				this.isok = n;
				// #ifdef  MP-WEIXIN
				// this.isok = 0
				// #endif
				this.$refs.popup.show();
				this.title_e = title;
				this.pid_d = pid;
				this.remark_k = remark;
				this.title_e = title;
			},
			setiscode() {
				this.codenum = 0
			},
			getinfo(id) {
				uni.showLoading({
					title: '加载中'
				});
				let other = uni.getStorageSync('other');
				let token = uni.getStorageSync('token');
				uni.request({
					url: this.javaserve + "/applets/jy/question",
					data: {
						id: id,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid'),
						token: uni.getStorageSync('token')
					},
					method: "GET",
					success: (res) => {
						console.log(res)
						if (res.data.status == 200) {

							this.building = res.data.data.project;
							this.data = res.data.data.question;
							this.recommends = res.data.data.recommends;
							this.relevant = res.data.data.relevant;
							this.staff = res.data.data.people;
							this.telphone = res.data.data.phone;
							this.pid = String(this.building.id)
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
							this.issure = true
							uni.hideLoading();
						}
					},
					fail: (error) => {
						console.log(error);
					}
				})
			},
			showAllwen() {
				uni.navigateTo({
					url: "../allwenda/allwenda?id=0"
				})
			}



		}
	}
</script>

<style lang="less">
	page {
		background-color: #fff;
		padding-bottom: 30rpx;
	}

	button::after {
		border: none;
	}

	.wendadetail {
		.toptitle {
			color: #fff;
			font-size: 29.88rpx;
			padding: 0 29.88rpx;
			line-height: 87.64rpx;
			background-color: #FFF;
			position: fixed;
			top: 0;
			width: 100%;
			z-index: 30000;

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

		.wen_top {
			padding-left: 30rpx;
			padding-right: 30rpx;
			box-sizing: border-box;

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
					margin-right: 20rpx;
					position: relative;
					top: -4rpx;
				}

				font-size: 30rpx;
				font-weight: bold;
				color: #323233;
				line-height: 45rpx;
				margin-bottom: 28rpx;
				margin-top: 34rpx;
			}

			.pro_one {
				width: 630rpx;
				height: 160rpx;
				background: #FFFFFF;
				box-shadow: 0rpx 0rpx 38rpx 2rpx rgba(0, 0, 0, 0.03);
				border-radius: 24rpx;
				padding-top: 24rpx;
				padding-left: 30rpx;
				padding-right: 30rpx;
				padding-bottom: 24rpx;
				margin-top: 40rpx;
				margin-bottom: 40rpx;

				image {
					width: 220rpx;
					height: 160rpx;
					border-radius: 12rpx;
					float: left;
					margin-right: 30rpx;
				}

				.right_pro {
					.pro_name {
						font-size: 30rpx;
						font-weight: 800;
						color: #0B0F12;
						line-height: 30rpx;
						margin-bottom: 4rpx;

						.status {
							width: 68rpx;
							height: 36rpx;
							background: #E6FAF0;
							border-radius: 6rpx;
							font-size: 22rpx;
							font-weight: 500;
							color: #20C657;
							line-height: 36rpx;
							float: right;
							text-align: center;
						}
					}

					.price {
						font-size: 32rpx;
						font-weight: 800;
						color: #FF6040;
						line-height: 56rpx;

						text {
							font-size: 26rpx;
							font-weight: 500;
							position: relative;
							top: -2rpx;
						}
					}

					.type {
						font-size: 24rpx;
						color: #646466;

						text {
							padding-left: 14rpx;
							padding-right: 14rpx;
						}
					}

					.tese {
						.zhuang {
							height: 36rpx;
							background: #EDF3FA;
							border-radius: 6rpx;
							font-size: 22rpx;
							font-weight: 500;
							color: #628BB9;
							line-height: 36rpx;
							margin-right: 12rpx;
							display: inline-block;
							text-align: center;
							padding-left: 8rpx;
							padding-right: 8rpx;
						}

						.other {
							font-size: 22rpx;
							font-weight: 500;
							color: #7D7D80;
							padding: 5rpx 14rpx;
							background-color: #F5F5F5;
							margin-right: 12rpx;
							border-radius: 6rpx;
						}
					}
				}
			}

			.huifu_btn {
				width: 100%;
				height: 80rpx;
				background: #F0F6FA;
				border-radius: 12rpx;
				font-size: 30rpx;
				font-weight: 400;
				color: #40A2F4;
				line-height: 80rpx;
				text-align: center;
				margin-bottom: 40rpx;
			}
		}

		.da {
			.top {
				width: 660rpx;
				height: 105rpx;
				border-top: 1rpx solid #F2F2F2;
				padding-top: 40rpx;

				.left_box {
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
								display: inline-block;
								position: relative;
								top: -2rpx;
							}
						}

						.pp {
							font-size: 24rpx;
							color: #7D7E80;
							line-height: 30rpx;

							text {
								color: #FF6A48;
							}
						}

					}
				}

				.right_btn {
					width: 140rpx;
					height: 52rpx;
					background: linear-gradient(270deg, #28C567, #81DB85);
					;
					border-radius: 26rpx;
					line-height: 52rpx;
					text-align: center;
					float: right;
					font-size: 24rpx;
					font-weight: 400;
					color: #FFFFFF;
					margin-top: 5rpx;
					padding-left: 0rpx;
					padding-right: 0rpx;
				}


			}

			.bottom {
				font-size: 28rpx;
				color: #4B4C4D;
				line-height: 46rpx;
				background-color: #F7F7F7;
				padding-left: 30rpx;
				padding-bottom: 26rpx;
				padding-top: 28rpx;
				border-radius: 24rpx;
				padding-right: 30rpx;
			}

			.time_box {
				width: 100%;
				height: 100rpx;

				.time {
					font-size: 24rpx;
					font-weight: 400;
					color: #969799;
					line-height: 96rpx;
					float: left;
				}

				.zan {
					float: right;
					display: flex;
					align-items: center;

					image {
						width: 32rpx;
						height: 32rpx;
						margin-right: 8rpx;
					}

					font-size: 22rpx;
					font-weight: 500;
					color: #38916C;
					line-height: 100rpx;
				}

				.nozan {
					float: right;
					display: flex;
					align-items: center;

					image {
						width: 32rpx;
						height: 32rpx;
						margin-right: 8rpx;
					}

					font-size: 22rpx;
					font-weight: 500;
					color:#969799;
					line-height: 100rpx;
				}
			}
		}

		.hui_bg {
			width: 100%;
			height: 20rpx;
			background: #F7F7F7;
		}

		//相关楼盘问答	
		.about_wen {
			padding-left: 30rpx;
			padding-right: 30rpx;
			box-sizing: border-box;
			width: 100%;
			padding-bottom: 40rpx;

			.title {
				font-size: 34rpx;
				font-weight: bold;
				color: #323233;
				line-height: 110rpx;
			}

			.wen_tit {
				.wen {
					width: 30rpx;
					height: 30rpx;
					background: linear-gradient(270deg, #FF4B2D, #FFB753);
					border-radius: 4rpx;
					font-size: 20rpx;
					font-weight: 500;
					color: #FFFFFF;
					display: inline-block;
					text-align: center;
					line-height: 30rpx;
					margin-right: 25rpx;
					position: relative;
					top: -4rpx;
				}

				font-size: 30rpx;
				color: #272B2E;
				line-height: 45rpx;
				margin-bottom: 40rpx;
				margin-top: 7rpx;
			}

			.btn {
				width: 100%;
				height: 80rpx;
				background: #E9F5EE;
				font-size: 30rpx;
				font-weight: 400;
				color: #38916C;
				line-height: 80rpx;
				text-align: center;
				border-radius: 12rpx;
				padding-left: 0rpx;
				padding-right: 0rpx;
			}
		}

		.about_lou {
			padding-bottom: 100rpx;
		}

		.see_box {
			background-color: #FFFFFF;
			padding: 0 30rpx;
			padding-bottom: 140rpx;

			.tit {
				color: #19191A;
				font-size: 34rpx;
				font-weight: bold;
				margin-bottom: 38rpx;
				padding-top: 36rpx;
			}
		}
	}
</style>
