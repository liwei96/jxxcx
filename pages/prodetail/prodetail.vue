<template>
	<view class="prodetail">
		<!-- <view class="toptitle">
			<view class="status_bar">
			</view>
			<navigator :url="`/pageA/content/content?id=${building.id}`" class="nav_top" open-type="navigate">
				<image src="../../static/all-back.png" mode=""></image>
				<text>{{building.name}}</text>
			</navigator>
		</view> -->
		<view class="pro_top">
			<view class="name">
				{{building.name}}
			</view>
			<view class="tese_line">
				<text class="tese" v-if="building.sale_state">{{building.sale_state}}</text>
				<text class="other" v-if="building.type">{{building.type}}</text>
				<text class="other" v-if="building.railways.length">{{building.railways[0]}}</text>
				<template v-if="building.features">
					<text class="other" v-for="(item,index) in building.features" :key="item.id"
						v-if="index<=3">{{item}}</text>
				</template>
			</view>
		</view>
		<view class="bg_hui"></view>
		<!-- 基本信息 -->
		<view class="xinxi">
			<view class="tit">
				基本信息
			</view>
			<view class="dan_price">
				<text class="left">参考单价：</text>
				<text class="jia_right">
					{{building.single_price}}
					<text class="dan">元/m²</text>
				</text>
			</view>
			<view class="zong_price">
				<text class="left">参考总价：</text>
				<text class="jia_right">
					{{building.total_price}}
					<text class="dan">万起</text>
				</text>
				<button open-type="getPhoneNumber"
					@getphonenumber="getPhoneNumber($event,building.id,'项目详情页+查低价',105,'查低价')" hover-class="none"
					v-if="!pass&&!weixin">
					<text class="cha">查底价</text>
				</button>
				<button @tap="baoMing(building.id,'项目详情页+查低价',105,'查低价',1)" v-if="pass||weixin">
					<text class="cha">查底价</text>
				</button>
			</view>
			<view class="type">
				<text class="left">类 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;型：</text>
				{{building.type}}
			</view>
			<view class="huxing">
				<text class="left">户 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;型：</text>
				<text class="con">{{building.house_types?building.house_types.join('、'):''}}</text>
				<view class="more" @tap="goHuxing(building.id)">
					更多户型
					<image src="../../static/content/right.png" mode=""></image>
				</view>
			</view>
			<view class="address">
				<text class="left">楼盘地址：</text>
				<text class="add">{{building.address}}</text>
			</view>

		</view>
		<view class="bg_hui"></view>
		<!-- 销售信息 -->
		<view class="sales">
			<view class="tit">
				销售信息
			</view>
			<view class="open_time">
				<text class="left">开盘时间：</text>
				<text class="time">
					{{building.open_time}}
				</text>
				<button open-type="getPhoneNumber"
					@getphonenumber="getPhoneNumber($event,building.id,'项目详情页+开盘通知',92,'开盘提醒我')" v-if="!pass&&!weixin">
					<text class="kai_btn">开盘通知</text>
				</button>
				<button @tap="baoMing(building.id,'项目详情页+开盘通知',92,'开盘提醒我',1)" v-if="pass||weixin">
					<text class="kai_btn">开盘通知</text>
				</button>
			</view>
			<view class="push_time">
				<text class="left">加推时间：</text>
				<text class="time">
					{{building.add_push_time!=null?building.add_push_time:''}}
				</text>
			</view>
			<view class="jiao_time">
				<text class="left">交房时间：</text>
				<text class="time">
					{{building.give_time}}
				</text>
			</view>
			<view class="yu_zheng">
				<text class="left">预 售 证：</text>
				<text class="zheng">
					{{building.pre_sale_license}}
				</text>
			</view>
			<view class="chan_nian">
				<text class="left">产权年限：</text>
				<text class="year">
					{{building.year}}年
				</text>
			</view>
			<view class="kai_shang">
				<text class="left">开 发 商：</text>
				<text class="shang">
					{{building.builder}}
				</text>
			</view>
		</view>
		<view class="bg_hui"></view>
		<!-- 建筑信息 -->
		<view class="jianzhu">
			<view class="tit">
				建筑信息
			</view>
			<view class="common">
				<text class="left">户型面积：</text>
				<text class="right">
					{{building.area}}m²
				</text>
			</view>
			<view class="common">
				<text class="left">建筑面积：</text>
				<text class="right">
					{{building.built_area}}m²
				</text>
			</view>
			<view class="common">
				<text class="left">容积率：</text>
				<text class="right">
					{{building.plot_ratio}}
				</text>
			</view>
			<view class="common">
				<text class="left">绿化率：</text>
				<text class="right">
					{{building.green_rate}}%
				</text>
			</view>
			<view class="common">
				<text class="left">层高：</text>
				<text class="right">
					{{building.floor_height}}米
				</text>
			</view>
			<view class="common">
				<text class="left">车位情况：</text>
				<text class="right">
					{{building.house_num}}户（{{building.parking_num}}个车位）
				</text>
			</view>
			<view class="common">
				<text class="left">装修状况：</text>
				<text class="right">
					{{building.decorate}}
				</text>
			</view>
			<view class="common">
				<text class="left">公交路线：</text>
				<text class="right">
					{{building.traffic}}
				</text>
			</view>
			<view class="common">
				<text class="left">物业费用：</text>
				<text class="right">
					{{building.fee}}元/m²月
				</text>
			</view>
			<view class="common">
				<text class="left">物业公司：</text>
				<text class="right">
					{{building.property_company}}
				</text>
			</view>
		</view>
		<view class="bg_hui"></view>
		<!-- 项目介绍  -->
		<view class="jieshao">
			<view class="tit">
				项目介绍
			</view>
			<view class="content">
				<text class="text">{{text}}</text>
				<text class="zhan" v-show="zhan" v-if="building.introduce.length>=82" @click="showHide">[展开]</text>
			</view>
		</view>
		<bottom :remark="'楼盘详情页+预约看房'" :point="103" :title="'预约看房'" :pid="building.id" :telphone="telphone"></bottom>

		<wyb-popup ref="popup" type="center" height="750" width="650" radius="12" :showCloseIcon="true"
			@hide="setiscode">
			<sign :type="codenum" @closethis="setpop" :title="title_e" :pid="pid_d" :remark="remark_k"
				:position="position_n" :isok="isok"></sign>
		</wyb-popup>

	</view>
</template>

<script>
	import bottom from '../../components/mine/bottom.vue';
	import wybPopup from '@/components/wyb-popup/wyb-popup.vue'
	import sign from '@/components/sign.vue'
	export default {
		data() {
			return {
				pass: false,
				text: '',
				building: {},
				text_all: '',
				zhan: true,

				codenum: 1,
				title_e: '',
				pid_d: '',
				remark_k: '',
				position_n: 0,
				telphone: '',
				isok: 0,
				weixin: false,
				bid: 0
			};
		},
		components: {
			bottom,
			wybPopup,
			sign,
		},
		onLoad(option) {
			console.log(option);
			this.bid = option.id
			this.getinfo(option.id);
			this.pass = uni.getStorageSync('pass')
			// #ifdef  MP-WEIXIN
			// this.weixin = true
			// #endif
		},
		methods: {
			setpop() {
				this.$refs.popup.hide()
			},
			async getPhoneNumber(e, pid, remark, point, title, type) {
				let that = this
				// #ifdef  MP-BAIDU
				if (e.detail.errMsg == 'getPhoneNumber:fail auth deny') {
					this.isok = 0
					that.baoMing(pid, remark, point, title, 0)
					if (type) {

					}
				} else {
					this.pass = true;
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
												// that.$refs.sign.tel = tel
												that.baoMing(pid, remark, point,
													title, 1)
												uni.request({
													url: "https://java.edefang.net/applets/jy/login",
													method: "POST",
													header: {
														"Content-Type": "application/x-www-form-urlencoded;charset=utf-8",
													},
													data: {
														bid:that.bid,
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
					that.baoMing(pid, remark, point, title, 0)
					if (type) {

					}
				} else {
					this.pass = true;
					uni.setStorageSync('pass', true)
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
											that.$refs.sign.tel = tel
											that.baoMing(pid, remark, point, title,
												1)
										}
									})

								}
							})
						}
					});
					this.isok = 1
				}
				// #endif
			},
			baoMing(pid, remark, point, title, n) {
				this.isok = n
				// #ifdef  MP-WEIXIN
				// this.isok = 0
				// #endif
				this.title_e = title;
				this.pid_d = pid;
				this.remark_k = remark;
				this.position_n = point;
				console.log(pid);
				this.$refs.popup.show();
			},
			setiscode() {
				this.codenum = 0
			},
			showHide() {
				this.text = this.text_all;
				this.zhan = false;
			},
			goHuxing(id) {
				uni.navigateTo({
					url: "../prohuxing/prohuxing?id=" + id
				})
			},
			getinfo(id) {
				uni.showLoading({
					title: '加载中'
				});

				let that = this;
				let token = uni.getStorageSync("token");
				uni.request({
					url: this.javaserve + '/applets/jy/building/abstract',
					data: {
						id: id,
						token: token,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					method: "GET",
					success: (res) => {
						console.log(res, 888)
						if (res.data.status == 200) {
							let data = res.data.data
							this.building = data.building;
							uni.setNavigationBarTitle({
								title: data.building.name+'详情',
							});
							this.text_all = data.building.introduce;
							this.text = data.building.introduce.substring(0, 82);
							this.telphone = data.phone;
							uni.hideLoading();
							// #ifdef MP-BAIDU
							let header = data.header;
							swan.setPageInfo({
								title: header.title,
								keywords: header.keywords,
								description: header.description,
								// image: [that.building.img],
								success: res => {
									console.log('setPageInfo success', res);
								},
								fail: err => {
									console.log('setPageInfo fail', err);
								}
							})
							// #endif
						}
					}
				})
			}
		}
	}
</script>

<style lang="less">
	button::after {
		border: none;
	}

	button {
		float: right;
		background-color: #fff;
	}

	.prodetail {
		padding-bottom: 30rpx;
		.toptitle {
			color: #17181A;
			font-size: 29.88rpx;
			padding: 0 29.88rpx;
			line-height: 87.64rpx;
			background-color: #fff;
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

		.pro_top {
			background: #fff;
			padding-left: 30rpx;
			padding-right: 30rpx;
			padding-top: 40rpx;
			padding-bottom: 32rpx;

			.name {
				font-size: 40rpx;
				font-weight: 800;
				color: #121212;
				line-height: 40rpx;
				margin-bottom: 19rpx;
			}

			.tese_line {
				.tese {
					// width: 68rpx;
					// height: 36rpx;
					padding: 8rpx 12rpx;
					background: #E6FAF0;
					border-radius: 6rpx;
					font-size: 22rpx;
					font-weight: 500;
					color: #2AC66D;
					text-align: center;
					// line-height: 36rpx;
					display: inline-block;
					margin-right: 12rpx;
				}

				.other {
					font-size: 22rpx;
					font-weight: 500;
					color: #7D7D80;
					border-radius: 6rpx;
					padding: 8rpx 12rpx;
					background: #F5F5F5;
					margin-right: 12rpx;
				}
			}
		}

		.bg_hui {
			width: 100%;
			height: 20rpx;
			background-color: #F7F7F7;
		}

		.xinxi {
			height: 587rpx;
			width: 100%;
			padding-left: 30rpx;
			padding-right: 30rpx;
			box-sizing: border-box;
			background-color: #fff;

			.tit {
				font-size: 32rpx;
				font-weight: bold;
				color: #303233;
				line-height: 114rpx;
			}

			.dan_price {
				height: 94rpx;
				border-bottom: 1rpx solid #F2F2F2;
				border-top: 1rpx solid #F2F2F2;

				.left {
					font-size: 28rpx;
					font-weight: 500;
					color: #808080;
					line-height: 94rpx;
				}

				.jia_right {
					font-size: 32rpx;
					font-weight: bold;
					color: #FE582F;
					line-height: 94rpx;

					.dan {
						font-size: 18rpx;
						font-weight: 400;
						color: #FE582F;
						line-height: 94rpx;
					}
				}

			}

			.zong_price {
				height: 94rpx;
				border-bottom: 1rpx solid #F2F2F2;

				.left {
					font-size: 28rpx;
					font-weight: 500;
					color: #808080;
					line-height: 94rpx;
				}

				.jia_right {
					font-size: 32rpx;
					font-weight: bold;
					color: #FE582F;
					line-height: 94rpx;

					.dan {
						font-size: 18rpx;
						font-weight: 400;
						color: #FE582F;
						line-height: 94rpx;
					}
				}

				.cha {
					width: 148rpx;
					height: 56rpx;
					background: #E9F5EE;
					border-radius: 8rpx;
					display: inline-block;
					font-size: 26rpx;
					font-weight: 500;
					color: #38916C;
					text-align: center;
					line-height: 56rpx;
					float: right;
					margin-top: 20rpx;
				}
			}

			.type {
				height: 94rpx;
				border-bottom: 1rpx solid #F2F2F2;

				.left {
					font-size: 28rpx;
					font-weight: 500;
					color: #808080;
					line-height: 94rpx;
				}

				font-size: 28rpx;
				font-weight: 500;
				color: #323233;
				line-height: 94rpx;
			}

			.huxing {
				height: 94rpx;
				border-bottom: 1rpx solid #F2F2F2;

				.left {
					font-size: 28rpx;
					font-weight: 500;
					color: #808080;
					float: left;
					line-height: 94rpx;
				}

				.con {
					font-size: 28rpx;
					font-weight: 500;
					color: #323233;
					float: left;
					line-height: 94rpx;
				}

				.more {
					font-size: 28rpx;
					font-weight: 500;
					color: #5F7891;
					float: right;
					line-height: 94rpx;

					image {
						width: 24rpx;
						height: 24rpx;
					}
				}
			}

			.address {
				height: 94rpx;
				width: 100%;
				overflow: hidden;
				white-space: nowrap;
				text-overflow: ellipsis;

				.left {
					font-size: 28rpx;
					font-weight: 500;
					color: #808080;
					line-height: 94rpx;
				}

				.add {
					font-size: 28rpx;
					font-weight: 500;
					color: #323233;
					line-height: 94rpx;

				}
			}
		}

		//销售信息
		.sales {
			width: 100%;
			padding-left: 30rpx;
			padding-right: 30rpx;
			box-sizing: border-box;
			background-color: #fff;
			height: 683rpx;

			.tit {
				font-size: 32rpx;
				font-weight: bold;
				color: #2F3133;
				line-height: 114rpx;
			}

			.open_time {
				height: 94rpx;
				border-bottom: 1rpx solid #F2F2F2;
				border-top: 1rpx solid #F2F2F2;

				.left {
					font-size: 28rpx;
					font-weight: 500;
					color: #808080;
					line-height: 94rpx;
				}

				.time {
					font-size: 28rpx;
					color: #323233;
					line-height: 94rpx;
				}

				.kai_btn {
					width: 148rpx;
					height: 56rpx;
					background: #E9F5EE;
					border-radius: 8rpx;
					display: inline-block;
					font-size: 26rpx;
					font-weight: 500;
					color: #38916C;
					text-align: center;
					line-height: 56rpx;
					float: right;
					margin-top: 20rpx;
				}
			}

			.push_time {
				height: 94rpx;
				border-bottom: 1rpx solid #F2F2F2;

				.left {
					font-size: 28rpx;
					font-weight: 500;
					color: #808080;
					line-height: 94rpx;
				}

				.time {
					font-size: 28rpx;
					color: #323233;
					line-height: 94rpx;
				}
			}

			.jiao_time {
				height: 94rpx;
				border-bottom: 1rpx solid #F2F2F2;

				.left {
					font-size: 28rpx;
					font-weight: 500;
					color: #808080;
					line-height: 94rpx;
				}

				.time {
					font-size: 28rpx;
					color: #323233;
					line-height: 94rpx;
				}
			}

			.yu_zheng {
				height: 94rpx;
				border-bottom: 1rpx solid #F2F2F2;

				.left {
					font-size: 28rpx;
					font-weight: 500;
					color: #808080;
					line-height: 94rpx;
				}

				.zheng {
					font-size: 28rpx;
					color: #323233;
					line-height: 94rpx;
				}
			}

			.chan_nian {
				height: 94rpx;
				border-bottom: 1rpx solid #F2F2F2;

				.left {
					font-size: 28rpx;
					font-weight: 500;
					color: #808080;
					line-height: 94rpx;
				}

				.year {
					font-size: 28rpx;
					color: #323233;
					line-height: 94rpx;
				}
			}

			.kai_shang {
				height: 94rpx;
				border-bottom: 1rpx solid #F2F2F2;

				.left {
					font-size: 28rpx;
					font-weight: 500;
					color: #808080;
					line-height: 94rpx;
				}

				.shang {
					font-size: 28rpx;
					color: #323233;
					line-height: 94rpx;
				}
			}
		}

		//建筑信息
		.jianzhu {
			width: 100%;
			padding-left: 30rpx;
			padding-right: 30rpx;
			box-sizing: border-box;
			background-color: #fff;
			height: 1062rpx;

			.tit {
				font-size: 32rpx;
				font-weight: bold;
				color: #2F3133;
				line-height: 114rpx;
			}

			.common {
				height: 94rpx;
				border-bottom: 1rpx solid #F2F2F2;
				width: 100%;
				overflow: hidden;
				text-overflow: ellipsis;
				white-space: nowrap;

				.left {
					font-size: 28rpx;
					font-weight: 500;
					color: #808080;
					line-height: 94rpx;
				}

				.right {
					font-size: 28rpx;
					color: #323233;
					line-height: 94rpx;
				}
			}
		}

		//项目介绍
		.jieshao {
			width: 100%;
			padding-left: 30rpx;
			padding-right: 30rpx;
			box-sizing: border-box;
			background-color: #fff;
			height: auto;
			margin-bottom: 120rpx;

			.tit {
				font-size: 32rpx;
				font-weight: bold;
				color: #2F3133;
				line-height: 114rpx;
			}

			.content {
				.text {
					font-size: 28rpx;
					font-weight: 500;
					color: #646566;
					line-height: 53rpx;
				}

				.zhan {
					font-size: 30rpx;
					font-weight: 500;
					color: #5F7891;
					line-height: 53rpx;
				}
			}
		}

	}
</style>
