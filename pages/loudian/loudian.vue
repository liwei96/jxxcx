<template>
	<view class="loudian">
		<!-- <view class="toptitle">
			<view class="status_bar">
			</view>
			<navigator :url="`/pageA/content/content?id=${project_id}`" class="nav_top" open-type="navigate">
				<image src="../../static/all-back.png" mode=""></image>
				<text>楼盘点评</text>
			</navigator>
		</view> -->
		<view class="dian_list">
			<template v-if="data.length>0">
				<view class="dian_one" v-for="item in data" :key="item.id">
					<navigator :url="`../diandetail/diandetail/?id=${item.id}`">
						<view class="top">
							<image src="../../static/content/ping_img.png" mode=""></image>
							<view class="right_tel">
								<text class="tel">{{item.mobile}}</text>
								<view class="rate">
									<uni-rate v-model="item.score" :margin="7" color="#E8EBED" active-color="#FF7519"
										:readonly="true" :size="14"></uni-rate>
								</view>
							</view>
						</view>
						<text class="con">{{item.content}}</text>
					</navigator>
					<view class="gong">
						<view class="left">
							<text class="time">{{item.time}}</text>
							<button class="shan" v-if="!pass&&item.mine==1" open-type="getPhoneNumber"
								@getphonenumber="getPhoneNumber($event,1)" @tap="did = item.id">删除</button>
							<view class="shan" v-if="pass&&item.mine==1" @tap="deletePing(item.id)">
								删除
							</view>
						</view>
						<view class="zan">
							<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber($event,2)"
								@tap="did = item.id" v-if="!pass&&!weixin">
								<view class="zan_box_no" v-if="item.my_like==0">
									<image src="../../static/content/no_zan.png" mode=""></image>
									{{item.like_count}}
								</view>
							</button>
							<view class="zan_box_no" v-if="item.my_like==0 && (pass||weixin)" @tap="getLike(item.id)">
								<image src="../../static/content/no_zan.png" mode=""></image>
								{{item.like_count}}
							</view>

							<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber($event,2)"
								@tap="did = item.id" v-if="!pass&&!weixin">
								<view class="zan_box_zan" v-if="item.my_like==1">
									<image src="../../static/content/zan.png" mode=""></image>
									{{item.like_count}}
								</view>
							</button>
							<view class="zan_box_zan" v-if="item.my_like==1 && (pass||weixin)" @tap="getLike(item.id)">
								<image src="../../static/content/zan.png" mode=""></image>
								{{item.like_count}}
							</view>
							<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber($event,3)"
								@tap="did = item.id" v-if="!pass&&!weixin">
								<view class="dianping">
									<image src="../../static/liu.png" mode=""></image>
									{{item.children.length}}
								</view>
							</button>
							<view class="dianping" @tap="goHuifu(item.id)" v-if="pass||weixin">
								<image src="../../static/liu.png" mode=""></image>
								{{item.children.length}}
							</view>
						</view>
					</view>
				</view>
			</template>
			<template v-else>
				<view class="zanwu">
					暂无评论，快来评论吧
				</view>
			</template>

		</view>
		<!-- 传入项目id 先判断是否登录-->
		<button open-type="getPhoneNumber" hover-class="none" @getphonenumber="getPhoneNumber($event,4)"
			v-if="!pass&&!weixin">
			<view class="white_ping">
				<image src="../../static/other/white.png" mode=""></image>
			</view>
		</button>
		<view class="white_ping" v-if="pass||weixin" @tap="godian">
			<image src="../../static/other/white.png" mode=""></image>
		</view>

		<bottom :remark="'项目楼盘点评页+预约看房'" :point="103" :title="'预约看房'" :pid="project_id" :telphone="telphone">
		</bottom>
		<mytoast :txt="msg" ref="msg"></mytoast>
		<!-- 登录弹框 -->
		<wyb-popup ref="login" type="bottom" height="570" width="650" radius="0" :showCloseIcon="true"
			closeIconSize="32" @hide="setiscode">
			<login @login="logined"></login>
		</wyb-popup>
	</view>
</template>

<script>
	import uniRate from '@/components/uni-rate/uni-rate.vue';
	import bottom from '../../components/mine/bottom.vue';
	import mytoast from "@/components/mytoast/mytoast.vue"
	import login from "@/components/login.vue";
	export default {
		data() {
			return {
				ceshi1: 3,
				value: 3,
				data: [],
				project_id: "",
				telphone: '',
				page: 1,
				hua: true,
				did: 0,
				msg: '',
				pass: false,
				weixin: false,
				typebtn: ''
			};
		},
		components: {
			uniRate,
			bottom,
			mytoast,
			login
		},
		onLoad(option) {
			this.project_id = option.id;
			let page = this.page;
			this.getdata(option.id, page);
			this.pass = uni.getStorageSync('pass')
			// #ifdef  MP-WEIXIN
			// this.weixin = true
			// #endif
		},
		onReachBottom() {
			let id = this.project_id;
			if (this.hua == true) {
				this.getmore(id);
			}
		},
		methods: {
			logined() {
				let that = this
				this.pass = true;
				this.$refs.login.hide();
				if (this.typebtn == 1) { //删除
					that.deletePing(that.did)
				} else if (this.typebtn == 2) { //点赞
					that.getLike(that.did)
				} else if (this.typebtn == 3) { //跳回复
					that.goHuifu(that.did)
				} else if (this.typebtn == 4) { //右下角跳写点评
					that.godian()
				}
			},
			goHuifu(id) {
				//pid 项目id id 评价id
				let token = uni.getStorageSync("token");
				if (token) {
					uni.navigateTo({
						url: "/pages/huifu/huifu?id=" + id + "&pid=" + this.project_id
					})
				} else {
					this.$refs.msg.show();
					this.msg = "请先登录"
					let url = '/pages/loudian/loudian?id=' + this.project_id
					uni.setStorageSync('backurl', url)
					uni.navigateTo({
						url: '/pages/login/login'
					})
				}

			},
			getPhoneNumber(e, type) {
				let that = this
				// #ifdef  MP-BAIDU
				if (e.detail.errMsg == 'getPhoneNumber:fail auth deny') {
					let token = uni.getStorageSync('token')
					if (token) {
						if (type == 1) { //删除
							that.deletePing(that.did)
						} else if (type == 2) { //点赞
							that.getLike(that.did)
						} else if (type == 3) { //跳回复
							that.goHuifu(that.did)
						} else if (type == 4) { //右下角跳写点评
							that.godian()
						}
					} else {
						this.typebtn = type
						this.$refs.login.show();
						// this.isok = 0
						// let url = '/pages/loudian/loudian?id=' + this.project_id
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
						if (type == 1) { //删除
							that.deletePing(that.did)
						} else if (type == 2) { //点赞
							that.getLike(that.did)
						} else if (type == 3) { //跳回复
							that.goHuifu(that.did)
						} else if (type == 4) { //右下角跳写点评
							that.godian()
						}
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
														uni.setStorageSync(
															"token",
															res.data
															.data);
														if (type ==
															1) { //删除 
															that.deletePing(
																that
																.did)
														} else if (type ==
															2) { //点赞
															that.getLike(
																that
																.did)
														} else if (type ==
															3) { //跳回复
															that.goHuifu(
																that
																.did)
														} else if (type ==
															4) { //右下角跳写点评
															that.godian()
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
						if (type == 1) { //删除
							that.deletePing(that.did)
						} else if (type == 2) { //点赞
							that.getLike(that.did)
						} else if (type == 3) { //跳回复
							that.goHuifu(that.did)
						} else if (type == 4) { //右下角跳写点评
							that.godian()
						}
					} else {
						this.isok = 0
						let url = '/pages/loudian/loudian?id=' + this.project_id
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
						if (type == 1) { //删除
							that.deletePing(that.did)
						} else if (type == 2) { //点赞
							that.getLike(that.did)
						} else if (type == 3) { //跳回复
							that.goHuifu(that.did)
						} else if (type == 4) { //右下角跳写点评
							that.godian()
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
														if (type ==
															1) { //删除 
															that.deletePing(
																that
																.did)
														} else if (type ==
															2) { //点赞
															that.getLike(
																that
																.did)
														} else if (type ==
															3) { //跳回复
															that.goHuifu(
																that
																.did)
														} else if (type ==
															4) { //右下角跳写点评
															that.godian()
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
			deletePing(id) {
				let token = uni.getStorageSync("token");
				if (token) {
					uni.request({
						url: this.javaserve + "/applets/jy/comment/delete",
						method: "POST",
						data: {
							token: token,
							id: id,
							other: uni.getStorageSync('other'),
							uuid: uni.getStorageSync('uuid')
						},
						header: {
							'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
						},
						success: (res) => {
							if (res.data.status == 200) {
								this.$refs.msg.show();
								this.msg = res.data.data;
								let page = this.page;
								this.getdata(this.project_id, page)
							} else {
								console.log(res)
							}
						}

					})
				} else {
					this.$refs.msg.show();
					this.msg = "请先登录"
					let url = '/pages/loudian/loudian?id=' + this.project_id
					uni.setStorageSync('backurl', url)
					uni.navigateTo({
						url: '/pages/login/login'
					})

				}
			},
			getLike(id) {
				let token = uni.getStorageSync("token");
				if (token) {
					uni.request({
						url: this.javaserve + "/applets/jy/comment/like",
						data: {
							token: token,
							id: id,
							other: uni.getStorageSync('other'),
							uuid: uni.getStorageSync('uuid')
						},
						header: {
							'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
						},
						method: "POST",
						success: (res) => {
							if (res.data.status == 200) {
								this.$refs.msg.show();
								this.msg = res.data.data;
								let page = this.page;
								this.getdata(this.project_id, page)
							}
						}
					})
				} else {
					this.$refs.msg.show();
					this.msg = "请先登录"
					let url = '/pages/loudian/loudian?id=' + this.project_id
					uni.setStorageSync('backurl', url)
					uni.navigateTo({
						url: '/pages/login/login'
					})
				}
			},
			godian() {
				let token = uni.getStorageSync('token');
				if (token) {
					let url = '/pages/loudian/loudian?id=' + this.project_id
					uni.setStorageSync('backurl', url)
					let id = this.project_id;
					uni.navigateTo({
						url: '/pages/senddian/senddian?id=' + id
					})
				} else {
					this.$refs.msg.show();
					this.msg = "请先登录"
					let url = '/pages/loudian/loudian?id=' + this.project_id
					uni.setStorageSync('backurl', url)
					uni.navigateTo({
						url: '/pages/login/login'
					})
				}

			},
			getdata(id, page) {
				uni.showLoading({
					title: '加载中'
				});
				let token = uni.getStorageSync('token');
				let other = uni.getStorageSync('other');
				uni.request({
					url: this.javaserve + "/applets/jy/comments",
					data: {
						id: id, //项目id
						page: page, //第几页
						limit: 10, //每页几条
						token: token,
						other: other,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					method: "POST",
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					success: (res) => {
						if (res.data.status == 200) {
							console.log(res);
							this.data = res.data.data.data;
							this.telphone = res.data.data.phone;
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
							uni.hideLoading();
						}
					},
					fail: (error) => {
						console.log(error);
					}
				})
			},
			getmore(id) {
				this.page = this.page + 1;
				let token = uni.getStorageSync('token');
				let other = uni.getStorageSync('other');
				uni.request({
					url: this.javaserve + "/applets/jy/comments",
					data: {
						id: id, //项目id
						page: page, //第几页
						limit: 10, //每页几条
						token: token,
						other: other,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					method: "POST",
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					success: (res) => {
						if (res.data.status == 200) {
							console.log(res);
							this.data = this.data.concat(res.data.data.data);
							if (res.data.data.data.length == 0) {
								this.hua = false;
							}
						}
					},
					fail: (error) => {
						console.log(error);
					}
				})
			}

		}
	}
</script>

<style lang="less">
	page {
		background-color: #fff;
	}

	button {
		padding: 0;
	}

	button::after {
		border: none;
	}

	.loudian {

		.dian_list {
			padding-left: 30rpx;
			padding-right: 30rpx;
			box-sizing: border-box;
			background: #fff;
			padding-bottom: 130rpx;

			.zanwu {
				font-size: 26rpx;
				color: #000;
				height: 100rpx;
				line-height: 100rpx;
				text-align: center;
				margin-bottom: 20rpx;
			}

			.dian_one {
				margin-top: 30rpx;

				.top:after {
					content: '';
					height: 0;
					display: block;
					visibility: hidden;
					overflow: hidden;
					clear: both;
				}

				.top {
					margin-bottom: 20rpx;

					image {
						width: 73rpx;
						height: 73rpx;
						float: left;
						margin-right: 23rpx;
					}

					.right_tel {
						.tel {
							font-size: 32rpx;
							font-weight: 800;
							color: #14181A;
						}
					}
				}

				.con {
					font-size: 32rpx;
					color: #2E3133;
					line-height: 46rpx;
					display: -webkit-box;
					-webkit-box-orient: vertical;
					-webkit-line-clamp: 2;
					overflow: hidden;
				}

				.gong:after {
					content: '';
					height: 0;
					display: block;
					visibility: hidden;
					overflow: hidden;
					clear: both;
				}

				.gong {
					margin-top: 20rpx;

					.left {
						float: left;
						display: flex;

						.time {
							font-size: 24rpx;
							font-weight: 400;
							color: #949799;
						}

						.shan {
							font-size: 26rpx;
							font-weight: 400;
							color: #4487CA;
							margin-left: 20rpx;
							padding-left: 0rpx;
							padding-right: 0rpx;
							line-height: 30rpx;
						}
					}

					.zan {
						float: right;
						align-items: center;
						display: flex;

						.zan_box_no {
							display: flex;
							align-items: center;
							margin-right: 20rpx;

							image {
								width: 32rpx;
								height: 32rpx;
								margin-right: 11rpx;
							}

							font-size: 24rpx;
							font-weight: 400;
							color: #95989D;
							line-height: 24rpx;
						}

						.zan_box_zan {
							display: flex;
							align-items: center;
							margin-right: 20rpx;

							image {
								width: 32rpx;
								height: 32rpx;
								margin-right: 11rpx;
							}

							font-size: 24rpx;
							font-weight: 400;
							color: #2FCB72;
							line-height: 24rpx;
						}

						.dianping {
							display: flex;
							align-items: center;

							image {
								width: 32rpx;
								height: 32rpx;
								margin-right: 11rpx;
							}

							font-size: 24rpx;
							font-weight: 400;
							color: #95989D;
							line-height: 24rpx;
						}
					}
				}
			}
		}

		.white_ping {
			width: 100rpx;
			height: 100rpx;
			position: fixed;
			bottom: 204rpx;
			right: 30rpx;

			image {
				width: 100rpx;
				height: 100rpx;
			}
		}
	}
</style>
