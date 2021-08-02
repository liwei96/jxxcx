<template>
	<view class="loupkdetail">
		<!-- <view class="toptitle">
			<view class="status_bar">
			</view>
			<navigator open-type="navigateBack" class="nav_top">
				<image src="../../static/all-back.png" mode=""></image>
				<text>楼盘对比</text>
			</navigator>
		</view> -->
		<view class="project_two" v-show="hua_old_show">
			<view class="left_tit">
				<view class="tit_01">楼盘对比</view>
				<view class="tit_02">楼盘信息</view>
			</view>
			<view class="imgbox">
				<image src="../../static/other/alliance-vs.png" mode=""></image>
			</view>
			<view class="pro_one" v-for="item in data" :key="item.id">
				<view class="img_top">
					<image :src="item.img" mode=""></image>
				</view>
				<view class="pro_bot">
					<view class="name">{{item.name.substr(0,7)}}</view>
					<view class="tese">
						<text class="status"
							v-if="item.sale_state!==''&& item.sale_state!==null">{{item.sale_state}}</text>
						<text class="type" v-if="item.type!=='' && item.type!==null">{{item.type}}</text>
					</view>
					<view class="price">
						<text>{{item.single_price}}</text>元/m²
					</view>
					<view class="btnbox">
						<image src="../../static/other/pk-msg.png" mode="" @tap="gotalk(item)"></image>
						<image src="../../static/other/pktel.png" mode="" @tap="boTel(telphone)"></image>
					</view>
					<view class="bo_tel_btn" @tap="boTel(telphone)" v-if="false">
						<image src="../../static/other/pk_tel.png" mode=""></image>
						电话咨询
					</view>
				</view>
			</view>

		</view>
		<!-- 隐藏 -->
		<view class="yin" v-show="hua_show_yin">
			<view class="left_tit">
				<view class="tit_01">楼盘对比</view>
				<view class="tit_02">楼盘信息</view>
			</view>
			<view class="pro_one" v-for="item in data" :key="item.id">
				<view class="img_top">
					<image :src="item.img" mode=""></image>
				</view>
				<view class="pro_bot">
					<view class="name">{{item.name}}</view>
					<view class="btnbox">
						<image src="../../static/other/pk-msg.png" mode="" @tap="gotalk(item)"></image>
						<image src="../../static/other/pktel.png" mode="" @tap="boTel(telphone)"></image>
					</view>
					<view class="bo_tel_btn" @tap="boTel(telphone)" v-if="false">
						<image src="../../static/other/pk_tel.png" mode=""></image>
						电话咨询
					</view>
				</view>
			</view>

		</view>






		<!-- 楼盘信息 -->
		<view class="xinxi_list">
			<view class="left_nav">
				<view class="address_tit">
					楼盘地址
				</view>
				<view class="tese_tit">
					项目特色
				</view>
				<view class="sale_tit">
					销售状态
				</view>
				<view class="jian_tit">
					建筑类型
				</view>
				<view class="zheng_tit">
					预售许可
				</view>
				<view class="zhuang_tit">
					装修状况
				</view>
				<view class="gao_tit">
					层高
				</view>
				<view class="year_tit">
					产权年限
				</view>
				<view class="subway_tit">
					距地铁
				</view>
			</view>
			<view class="pk_one_project" v-for="item in data" :key="item.id">
				<view class="address">
					{{item.address}}
				</view>
				<view class="tese">
					<template v-for="(ite,key) in item.features">
						<text :key="key" v-if="key<2">{{ite}}</text>
					</template>
				</view>
				<view class="state">
					{{item.sale_state}}
				</view>
				<view class="type">
					{{item.type}}
				</view>
				<view class="zheng">
					{{item.pre_sale_license}}
				</view>
				<view class="zhuang">
					{{item.decorate}}
				</view>
				<view class="height">
					{{item.floor_height}}m
				</view>
				<view class="year">
					{{item.year}}年
				</view>
				<view class="subway">
					<text v-for="(ite,key) in item.railways" :key="'klj'+key">{{ite}}</text>
				</view>
			</view>

		</view>




		<!-- 销售信息 -->
		<view class="sale_title">
			销售信息
		</view>
		<view class="sale_list">
			<view class="left_sale">
				<view class="danjia_tit">
					参考单价
				</view>
				<view class="zong_tit">
					参考总价
				</view>
				<view class="kai_time">
					开盘时间
				</view>
				<view class="jiao_time">
					交房时间
				</view>
				<view class="kaifa">
					开发商
				</view>
				<view class="youhui">
					优惠信息
				</view>
			</view>
			<view class="pro_sale" v-for="item in data" :key="item.id">
				<view class="danjia_box">
					<view class="price">约{{item.single_price}}元/m²</view>
					<button class="btn" open-type="getPhoneNumber" hover-class="none" v-if="!pass&&!weixin"
						@getphonenumber="getPhoneNumber($event,item.id,'楼盘pk详情页+咨询底价',105,'咨询楼盘底价')">咨询底价</button>
					<view class="btn" v-if="pass||weixin" @tap="baoMing(item.id,'楼盘pk详情页+咨询底价',105,'咨询楼盘底价',1)">咨询底价
					</view>
				</view>
				<view class="zongjia">
					{{item.total_price}}
				</view>
				<view class="kaipan">
					{{item.open_time}}
				</view>
				<view class="jiaotime">
					{{item.give_time}}
				</view>
				<view class="kaifa">
					{{item.builder}}
				</view>
				<view class="youhui">
					{{item.preferential}}
				</view>
			</view>

		</view>

		<!-- 建筑信息 -->
		<view class="jian_title">
			建筑信息
		</view>
		<view class="jianzhu_box">
			<view class="jian_left">
				<view class="humian">
					户型面积
				</view>
				<view class="huxing">
					楼盘户型
				</view>
				<view class="jian_mian">
					建筑面积
				</view>
				<view class="wufei">
					物业费用
				</view>
				<view class="rongji">
					容积率
				</view>
				<view class="wuye_gong">
					物业公司
				</view>
				<view class="lvhua">
					绿化率
				</view>
				<view class="chewei">
					车位情况
				</view>

			</view>
			<view class="jian_right" v-for="item in data" :key="item.id">
				<view class="humian">
					{{item.area}}m²
				</view>
				<view class="huxing">
					<view class="shi">
						{{item.house_types.join(',')}}
					</view>
					<view class="more">
						<navigator :url="`../prohuxing/prohuxing?id=${item.id}`">
							更多户型
							<image src="../../static/other/fan.png"></image>
						</navigator>
					</view>
				</view>
				<view class="jian_mian">
					{{item.built_area}}m²
				</view>
				<view class="wufei">
					{{item.fee}}元/m²月
				</view>
				<view class="rongji">
					{{item.plot_ratio}}
				</view>
				<view class="wuye_gong">
					{{item.property_company}}
				</view>
				<view class="lvhua">
					{{item.green_rate}}%
				</view>
				<view class="chewei">
					{{item.parking_num}}个
				</view>
			</view>

		</view>

		<button class="button" open-type="getPhoneNumber" hover-class="none" v-if="!pass&&!weixin"
			@getphonenumber="getPhoneNumber($event,0,'楼盘pk详情页+咨询详细楼盘信息',90,'咨询详细楼盘信息')">
			咨询详细楼盘信息
		</button>
		<view class="button" v-if="pass||weixin" @tap="baoMing(0,'楼盘pk详情页+咨询详细楼盘信息',90,'咨询详细楼盘信息',1)">
			咨询详细楼盘信息
		</view>
		<view class="other">
			<view class="tit">
				猜你喜欢
			</view>
			<twosee :recommends="recommends"></twosee>
		</view>
		<bottom :remark="'楼盘pk详情页+预约看房'" :point="103" :title="'预约看房'" :pid="pid" ref="bomm" :telphone="telphone">
		</bottom>

		<wyb-popup ref="popup" type="center" height="750" width="650" radius="12" :showCloseIcon="true"
			@hide="setiscode">
			<sign :type="codenum" @closethis="setpop" :title="title_e" :pid="pid_d" :remark="remark_k"
				:position="position_n" :isok="isok"></sign>
		</wyb-popup>
	</view>
</template>

<script>
	import twosee from '../../components/pros.vue';
	import bottom from '../../components/mine/bottom.vue'
	import wybPopup from "@/components/wyb-popup/wyb-popup.vue"
	import sign from '@/components/sign.vue'
	export default {
		data() {
			return {
				title: '猜你喜欢',
				hua_old_show: true,
				hua_show_yin: false,

				flag: 0,
				text: '',
				lastX: 0,
				lastY: 0,
				data: [],
				recommends: [],
				common: {},

				codenum: 1,
				title_e: '',
				pid_d: 0,
				remark_k: '',
				position_n: 0,
				telphone: '',
				pid: "0",

				tel: '4009669995',
				isok: 0,
				pass: false,
				weixin: false,
				pksid: 152
			};
		},
		onLoad(option) {
			console.log(option);
			this.getdata(option.id);
			this.pass = uni.getStorageSync('pass')
			// #ifdef  MP-WEIXIN
			// this.weixin = true
			// #endif
		},
		onPageScroll(e) {
			if (e.scrollTop >= 50) {
				this.hua_old_show = false;
				this.hua_show_yin = true;
			} else {
				this.hua_old_show = true;
				this.hua_show_yin = false;
			}

		},
		components: {
			twosee,
			bottom,
			wybPopup,
			sign
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
					this.pass = true
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
			baoMing(pid, msg, point, title, n) {
				this.isok = n;
				// #ifdef  MP-WEIXIN
				// this.isok = 0
				// #endif
				this.pid_d = pid;
				this.position_n = point,
					this.title_e = title;
				this.remark_k = msg;
				console.log(this.pid_d);
				this.$refs.popup.show();
			},
			setiscode() {
				this.codenum = 0
			},
			boTel(tel) {
				uni.makePhoneCall({
					phoneNumber: tel,
					success: function() {
						console.log('拨打电话');
					} //仅为示例
				});
			},
			gotalk(row) {
				this.$refs.bomm.register(row.id, 1)
				let id = String(this.pksid)
				let pid = row.id
				uni.navigateTo({
					url: '/pages/talk/talk?id=' + id + '&bid=' + pid
				})
			},
			getdata(id) {
				let city = uni.getStorageSync('city')
				let other = uni.getStorageSync('other');
				let token = uni.getStorageSync('token');
				uni.request({
					url: this.javaserve + '/applets/jy/compare',
					method: "POST",
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					data: {
						ids: id,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					success: (res) => {
						console.log(res)
						if (res.data.status == 200) {
							this.data = res.data.data.projects;
							this.recommends = res.data.data.recommends;
							this.telphone = res.data.data.phone;
							//#ifdef MP-BAIDU
							let header = res.data.data.head
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
						}
					}
				})
			},
			handletouchmove: function(event) {
				// console.log(event)
				if (this.flag !== 0) {
					return;
				}
				let currentX = event.touches[0].pageX;
				let currentY = event.touches[0].pageY;
				let tx = currentX - this.lastX;
				let ty = currentY - this.lastY;
				let text = '';
				this.mindex = -1;
				//左右方向滑动
				if (Math.abs(tx) > Math.abs(ty)) {
					if (tx < 0) {
						text = '向左滑动';
						this.flag = 1;
						//	this.getList();  //调用列表的方法
					} else if (tx > 0) {
						text = '向右滑动';
						this.flag = 2;
					}
				}
				//上下方向滑动
				else {
					if (ty < 0) {
						text = '向上滑动';
						this.flag = 3;
						console.log('向上滑动')
						this.hua_old_show = false;
						this.hua_show_yin = true;
						//	this.getList();  //调用列表的方法
					} else if (ty > 0) {
						text = '向下滑动';
						this.flag = 4;
						this.hua_old_show = true;
						this.hua_show_yin = false;
						console.log('向下滑动')
					}
				}

				//将当前坐标进行保存以进行下一次计算
				this.lastX = currentX;
				this.lastY = currentY;
				this.text = text;
			},
			handletouchstart: function(event) {
				// console.log(event)
				this.lastX = event.touches[0].pageX;
				this.lastY = event.touches[0].pageY;
			},
			handletouchend: function(event) {
				this.flag = 0;
				this.text = '没有滑动';
			},
		}
	}
</script>

<style lang="less">
	page {
		background: #fff;
	}

	button::after {
		border: none;
	}

	.loupkdetail {
		.toptitle {
			color: #D4D7D9;
			font-size: 29.88rpx;
			padding: 0 29.88rpx;
			line-height: 87.64rpx;
			background-color: #fff;
			position: fixed;
			top: 0;
			background: #fff;
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
					color: #17191A;
				}
			}
		}

		.project_two {
			width: 100%;
			height: 380rpx;
			padding-top: 40rpx;
			position: fixed;
			top: 0rpx;
			background: #fff;

			.left_tit {
				width: 180rpx;
				height: 420rpx;
				float: left;

				.tit_01 {
					font-size: 26rpx;
					font-weight: 500;
					color: #646566;
					margin-top: 115rpx;
					text-align: center;
				}

				.tit_02 {
					font-size: 30rpx;
					font-weight: 500;
					color: #121212;
					margin-top: 190rpx;
					text-align: center;
				}
			}

			.imgbox {
				position: absolute;
				width: 80rpx;
				height: 80rpx;
				border-radius: 50%;
				box-shadow: 0px 0px 19rpx 1rpx rgba(0, 0, 0, 0.04);
				display: flex;
				align-items: center;
				background-color: #FFFFFF;
				justify-content: center;
				left: 398rpx;
				top: 190rpx;

				image {
					width: 52rpx;
					height: 52rpx;
				}
			}

			.pro_one {
				width: 240rpx;
				height: 340rpx;
				float: left;
				box-shadow: 0px 0px 19px 1px rgba(0, 0, 0, 0.04);
				margin-right: 40rpx;
				border-radius: 16rpx;
				.img_top {
					image {
						width: 240rpx;
						height: 132rpx;
						border-radius: 16rpx 16rpx 0px 0px;
					}
				}

				.pro_bot {
					height: 208rpx;
					width: 216rpx;
					padding-left: 24rpx;

					.name {
						font-size: 26rpx;
						font-weight: 500;
						color: #272B2E;
					}

					.tese {
						.status {
							width: 56rpx;
							height: 28rpx;
							background: #E9F7EA;
							border-radius: 4rpx;
							font-size: 20rpx;
							font-weight: 500;
							color: #2AC66D;
							text-align: center;
							line-height: 28rpx;
							display: inline-block;
							margin-right: 10rpx;
						}

						.type {
							width: 56rpx;
							height: 28rpx;
							background: rgba(245, 245, 245, 1);
							border-radius: 4rpx;
							font-size: 20rpx;
							font-weight: 500;
							color: rgba(125, 126, 128, 1);
							text-align: center;
							line-height: 28rpx;
							display: inline-block;
						}
					}

					.price {
						font-size: 20rpx;
						color: #FF6A48;
						line-height: 48rpx;
						font-weight: bold;

						text {
							font-size: 30rpx;
						}
					}

					.btnbox {
						display: flex;
						justify-content: space-around;

						image {
							width: 56rpx;
							height: 56rpx;
						}
					}

					.bo_tel_btn {
						width: 192rpx;
						height: 48rpx;
						background: linear-gradient(-90deg, #FF986A, #FF4C4C);
						border-radius: 8rpx;
						font-size: 24rpx;
						font-weight: 400;
						color: #FFFFFF;
						display: flex;
						align-items: center;
						justify-content: center;

						image {
							width: 26rpx;
							height: 26rpx;
							margin-right: 9rpx;
						}
					}
				}
			}
		}

		.yin {
			width: 100%;
			height: 340rpx;
			padding-top: 40rpx;
			position: fixed;
			top: 0rpx;
			background: #fff;
			z-index: 150;

			.left_tit {
				width: 180rpx;
				height: 420rpx;
				float: left;

				.tit_01 {
					font-size: 26rpx;
					font-weight: 500;
					color: #646566;
					margin-top: 115rpx;
					text-align: center;
				}

				.tit_02 {
					font-size: 30rpx;
					font-weight: 500;
					color: #121212;
					margin-top: 111rpx;
					text-align: center;
				}
			}

			.pro_one {
				width: 240rpx;
				height: 260rpx;
				float: left;
				box-shadow: 0px 0px 19px 1px rgba(0, 0, 0, 0.04);
				margin-right: 40rpx;
				border-radius: 12rpx;
				.btnbox {
					display: flex;
					justify-content: space-around;

					image {
						width: 56rpx;
						height: 56rpx;
					}
				}

				.img_top {
					width: 240rpx;
					height: 132rpx;
					border-radius: 12rpx 12rpx 0px 0px;

					image {
						width: 240rpx;
						height: 132rpx;
						border-radius: 12rpx 12rpx 0px 0px;
					}
				}

				.pro_bot {
					height: 128rpx;
					width: 216rpx;
					padding-left: 24rpx;

					.name {
						font-size: 26rpx;
						font-weight: 500;
						color: #323233;
						line-height: 58rpx;
						margin-bottom: 3rpx;
					}

					.bo_tel_btn {
						width: 192rpx;
						height: 48rpx;
						background: linear-gradient(-90deg, #FF986A, #FF4C4C);
						border-radius: 8rpx;
						font-size: 24rpx;
						font-weight: 400;
						color: #FFFFFF;
						display: flex;
						align-items: center;
						justify-content: center;

						image {
							width: 26rpx;
							height: 26rpx;
							margin-right: 9rpx;
						}
					}
				}
			}
		}

		.xinxi_list {
			width: 100%;
			height: 940rpx;
			display: flex;
			margin-top: 440rpx;

			.left_nav {
				.address_tit {
					width: 180rpx;
					height: 119rpx;
					background: #F7F7F7;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 119rpx;
					text-align: center;
					border-bottom: 1rpx solid #EDEDED;
				}

				.tese_tit {
					width: 180rpx;
					height: 105rpx;
					background: #F7F7F7;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 119rpx;
					text-align: center;
					border-bottom: 1rpx solid #EDEDED;
				}

				.sale_tit {
					width: 180rpx;
					height: 99rpx;
					background: #F7F7F7;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 119rpx;
					text-align: center;
					border-bottom: 1rpx solid #EDEDED;
				}

				.jian_tit {
					width: 180rpx;
					height: 89rpx;
					background: #F7F7F7;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 119rpx;
					text-align: center;
					border-bottom: 1rpx solid #EDEDED;
				}

				.zheng_tit {
					width: 180rpx;
					height: 119rpx;
					background: #F7F7F7;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 119rpx;
					text-align: center;
					border-bottom: 1rpx solid #EDEDED;
				}

				.zhuang_tit {
					width: 180rpx;
					height: 99rpx;
					background: #F7F7F7;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 119rpx;
					text-align: center;
					border-bottom: 1rpx solid #EDEDED;
				}

				.gao_tit {
					width: 180rpx;
					height: 99rpx;
					background: #F7F7F7;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 119rpx;
					text-align: center;
					border-bottom: 1rpx solid #EDEDED;
				}

				.year_tit {
					width: 180rpx;
					height: 99rpx;
					background: #F7F7F7;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 99rpx;
					text-align: center;
					border-bottom: 1rpx solid #EDEDED;
				}

				.subway_tit {
					width: 180rpx;
					height: 99rpx;
					background: #F7F7F7;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 119rpx;
					text-align: center;
					border-bottom: 1rpx solid #EDEDED;
				}

			}

			.pk_one_project {
				.address {
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					width: 252rpx;
					height: 119rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					//margin-top: 24rpx;
					margin-left: 23rpx;
				}

				.tese {
					width: 256rpx;
					height: 105rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					padding-left: 19rpx;

					text {
						font-size: 22rpx;
						font-weight: 500;
						color: #7D7E80;
						padding: 6rpx 14rpx;
						background: rgba(245, 245, 245, 1);
						display: inline-block;
						margin-right: 10rpx;
						margin-top: 35rpx;
					}
				}

				.state {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.type {
					width: 275rpx;
					height: 89rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.zheng {
					width: 275rpx;
					height: 89rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 32rpx;
					text-align: center;
					padding-top: 30rpx;
				}

				.zhuang {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.height {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.year {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.subway {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}
			}

			.pk_two_project {
				.address {
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					width: 280rpx;
					height: 95rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					margin-top: 24rpx;
					margin-left: 23rpx;
				}

				.tese {
					width: 294rpx;
					height: 105rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					padding-left: 19rpx;

					text {
						font-size: 22rpx;
						font-weight: 500;
						color: #7D7E80;
						padding: 6rpx 14rpx;
						background: rgba(245, 245, 245, 1);
						display: inline-block;
						margin-right: 10rpx;
						margin-top: 35rpx;
					}
				}

				.state {
					width: 294rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.type {
					width: 294rpx;
					height: 89rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.zheng {
					width: 294rpx;
					height: 89rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 32rpx;
					text-align: center;
					padding-top: 30rpx;
				}

				.zhuang {
					width: 294rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.height {
					width: 294rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.year {
					width: 294rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.subway {
					width: 294rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}
			}
		}

		//	-- 销售信息 --
		.sale_title {
			height: 119rpx;
			line-height: 119rpx;
			font-size: 30rpx;
			font-weight: 500;
			color: #121212;
			padding-left: 30rpx;
		}

		.sale_list {
			display: flex;
			height: 660rpx;
			width: 100%;

			.left_sale {
				.danjia_tit {
					width: 180rpx;
					height: 139rpx;
					background: #F7F7F7;
					border-bottom: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 139rpx;
					text-align: center;
				}

				.zong_tit {
					width: 180rpx;
					height: 99rpx;
					background: #F7F7F7;
					border-bottom: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 99rpx;
					text-align: center;
				}

				.kai_time {
					width: 180rpx;
					height: 99rpx;
					background: #F7F7F7;
					border-bottom: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 99rpx;
					text-align: center;
				}

				.jiao_time {
					width: 180rpx;
					height: 99rpx;
					background: #F7F7F7;
					border-bottom: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 99rpx;
					text-align: center;
				}

				.kaifa {
					width: 180rpx;
					height: 119rpx;
					background: #F7F7F7;
					border-bottom: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 119rpx;
					text-align: center;
				}

				.youhui {
					width: 180rpx;
					height: 99rpx;
					background: #F7F7F7;
					border-bottom: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 99rpx;
					text-align: center;
				}
			}

			.pro_sale {
				.danjia_box {
					width: 275rpx;
					height: 139rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);

					.price {
						font-size: 26rpx;
						font-weight: 400;
						color: #FF6A48;
						line-height: 26rpx;
						line-height: 70rpx;
						text-align: center;
					}

					.btn {
						width: 140rpx;
						height: 48rpx;
						background: #D5EDDF;
						border-radius: 8rpx;
						font-size: 24rpx;
						font-family: Microsoft YaHei;
						font-weight: 400;
						color: #38916C;
						text-align: center;
						line-height: 48rpx;
						margin-left: 68rpx;
						padding-left: 0rpx;
						padding-right: 0rpx;
					}
				}

				.zongjia {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.kaipan {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.jiaotime {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.kaifa {
					width: 275rpx;
					height: 91rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 32rpx;
					text-align: center;
					padding-top: 28rpx;
				}

				.youhui {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					border-right: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #FE582F;
					line-height: 99rpx;
					text-align: center;
				}

			}

			.pro_sale_two {
				.danjia_box {
					width: 292rpx;
					height: 139rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);

					.price {
						font-size: 26rpx;
						font-weight: 400;
						color: #FF6A48;
						line-height: 26rpx;
						line-height: 70rpx;
						text-align: center;
					}

					.btn {
						width: 140rpx;
						height: 48rpx;
						background: #D5EDDF;
						border-radius: 8rpx;
						font-size: 24rpx;
						font-family: Microsoft YaHei;
						font-weight: 400;
						color: #38916C;
						text-align: center;
						line-height: 48rpx;
						margin-left: 78rpx;
					}
				}

				.zongjia {
					width: 292rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.kaipan {
					width: 292rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.jiaotime {
					width: 292rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 99rpx;
					text-align: center;
				}

				.kaifa {
					width: 292rpx;
					height: 91rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 32rpx;
					text-align: center;
					padding-top: 28rpx;
				}

				.youhui {
					width: 292rpx;
					height: 99rpx;
					border-bottom: 1rpx solid rgba(237, 237, 237, 1);
					font-size: 26rpx;
					font-weight: 500;
					color: #FE582F;
					line-height: 99rpx;
					text-align: center;
				}

			}
		}

		//建筑信息	
		.jian_title {
			height: 119rpx;
			line-height: 119rpx;
			font-size: 30rpx;
			font-weight: 500;
			color: #121212;
			padding-left: 30rpx;
		}

		.jianzhu_box {
			display: flex;

			.jian_left {
				.humian {
					width: 180rpx;
					height: 100rpx;
					background: #F7F7F7;
					border-bottom: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 100rpx;
					text-align: center;
				}

				.huxing {
					width: 180rpx;
					height: 118rpx;
					background: #F7F7F7;
					border-bottom: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 100rpx;
					text-align: center;
				}

				.jian_mian {
					width: 180rpx;
					height: 99rpx;
					background: #F7F7F7;
					border-bottom: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 100rpx;
					text-align: center;
				}

				.wufei {
					width: 180rpx;
					height: 99rpx;
					background: #F7F7F7;
					border-bottom: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 100rpx;
					text-align: center;
				}

				.rongji {
					width: 180rpx;
					height: 99rpx;
					background: #F7F7F7;
					border-bottom: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 100rpx;
					text-align: center;
				}

				.wuye_gong {
					width: 180rpx;
					height: 99rpx;
					background: #F7F7F7;
					border-bottom: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 100rpx;
					text-align: center;
					overflow: hidden;
					text-overflow:ellipsis;
					white-space: nowrap;
				}

				.lvhua {
					width: 180rpx;
					height: 100rpx;
					background: #F7F7F7;
					border-bottom: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 100rpx;
					text-align: center;
				}

				.chewei {
					width: 180rpx;
					height: 99rpx;
					background: #F7F7F7;
					border-bottom: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #7D7E80;
					line-height: 100rpx;
					text-align: center;
				}
			}

			.jian_right {
				.humian {
					width: 275rpx;
					height: 100rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 100rpx;
					text-align: center;
				}

				.huxing {
					width: 275rpx;
					height: 118rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					display: flex;
					justify-content: center;
					align-items: center;
					flex-direction: column;

					.shi {
						font-size: 26rpx;
						font-weight: 500;
						color: #272B2E;
						line-height: 26rpx;
						text-align: center;
					}

					.more {
						font-size: 24rpx;
						font-weight: 500;
						color: #5F7891;
						line-height: 24rpx;
						text-align: center;
						margin-top: 23rpx;

						image {
							width: 24rpx;
							height: 24rpx;
						}
					}

				}

				.jian_mian {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 100rpx;
					text-align: center;
				}

				.wufei {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 100rpx;
					text-align: center;
				}

				.rongji {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 100rpx;
					text-align: center;
				}

				.wuye_gong {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 100rpx;
					text-align: center;
					overflow: hidden;
					text-overflow:ellipsis;
					white-space: nowrap;
				}

				.lvhua {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 100rpx;
					text-align: center;
				}

				.chewei {
					width: 275rpx;
					height: 99rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 100rpx;
					text-align: center;
				}
			}

			.jian_right_r {
				.humian {
					width: 292rpx;
					height: 100rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 100rpx;
					text-align: center;
				}

				.huxing {
					width: 292rpx;
					height: 118rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					display: flex;
					justify-content: center;
					align-items: center;
					flex-direction: column;

					.shi {
						font-size: 26rpx;
						font-weight: 500;
						color: #272B2E;
						line-height: 26rpx;
						text-align: center;
					}

					.more {
						font-size: 24rpx;
						font-weight: 500;
						color: #5F7891;
						line-height: 24rpx;
						text-align: center;
						margin-top: 23rpx;

						image {
							width: 24rpx;
							height: 24rpx;
						}
					}

				}

				.jian_mian {
					width: 292rpx;
					height: 99rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 100rpx;
					text-align: center;
				}

				.wufei {
					width: 292rpx;
					height: 99rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 100rpx;
					text-align: center;
				}

				.rongji {
					width: 292rpx;
					height: 99rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 100rpx;
					text-align: center;
				}

				.wuye_gong {
					width: 292rpx;
					height: 99rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 100rpx;
					text-align: center;
					overflow: hidden;
					text-overflow:ellipsis;
					white-space: nowrap;
				}

				.lvhua {
					width: 292rpx;
					height: 99rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 100rpx;
					text-align: center;
				}

				.chewei {
					width: 292rpx;
					height: 99rpx;
					border-bottom: 1rpx solid #EDEDED;
					border-right: 1rpx solid #EDEDED;
					font-size: 26rpx;
					font-weight: 500;
					color: #272B2E;
					line-height: 100rpx;
					text-align: center;
				}
			}
		}

		.button {
			width: 600rpx;
			height: 72rpx;
			background: #D5EDDF;
			border-radius: 8rpx;
			font-size: 30rpx;
			font-weight: bold;
			color: #38916C;
			text-align: center;
			line-height: 72rpx;
			margin-top: 40rpx;
			margin-left: 75rpx;
			margin-bottom: 9rpx;
		}

		.about_lou {
			padding-bottom: 110rpx;
		}

	}

	.other {
		margin: 0 30rpx;
		margin-top: 58rpx;
		padding-bottom: 130rpx;

		.tit {
			color: #19191A;
			font-size: 34rpx;
			margin-bottom: 40rpx;
			font-weight: bold;
		}
	}
</style>
