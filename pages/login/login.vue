<template>
	<view class="login">
		<view class="login_box">
			<view class="title">
				<image src="../../static/other/login.png"></image>
			</view>
			<view class="tel">
				<input placeholder="输入手机号" v-model="telphone"
					placeholder-style="color:#969799;font-size:30rpx;line-height:128rpx" maxlength="11" />
			</view>
			<view class="yan">
				<input placeholder="输入验证码" v-model="code"
					placeholder-style="color:#969799;font-size:34rpx;line-height:128rpx;font-weight:500" />
				<text @tap="getCode">{{timetxt}}</text>
			</view>
			<view class="tishi">
				<checkbox-group @change="set">
				<checkbox value="cb" checked="true" color="#2AC66D" style="transform:scale(0.7)"/>
				</checkbox-group>
				<view class="txt">
					登录即表示您同意<text @tap="goserve">《家园服务协议》</text>
				</view>
			</view>
			<view :class="ok&&code&&telphone?'login active':'login'" @tap="getLogin">立即登录</view>
		</view>
		<toast ref="toast" :txt="msg"></toast>
	</view>
</template>

<script>
	import toast from "@/components/mytoast/mytoast.vue"
	export default {
		components: {
			toast
		},
		data() {
			return {
				telphone: "",
				code: "",
				second: 60,
				timetxt: "获取验证码",
				msg: "",
				istime: false,
				timer: null,
				ok: true
			};
		},
		onLoad() {

		},
		methods: {
			goserve() {
				uni.navigateTo({
					url:"/pages/serve/serve"
				})
			},
			set(e){
				if(e.detail.value.length==0) {
					this.ok = false
				}else{
					this.ok = true
				}
			},
			getCode() {
				let that = this;
				let phone = this.telphone;
				var pattern_phone = /^1[3-9][0-9]{9}$/;
				if (phone == "") {
					this.msg = "手机号不能为空"
					this.$refs.toast.show();
					return;
				} else if (!pattern_phone.test(phone)) {
					this.msg = "手机号码不合法"
					this.$refs.toast.show();
					return;
				}
				let kid = uni.getStorageSync("kid") || null;
				let other = uni.getStorageSync("other") || null;
				let ip = ''
				let city = uni.getStorageSync('city') || 1
				uni.request({
					url: that.putserve + `/getIp.php`,
					method: 'GET',
					success: (res) => {
						ip = res.data
						ip = ip.split('=')[1].split(':')[1]
						ip = ip.substring(1)
						ip = ip.slice(0, -3)
						ip=String(ip)
						uni.request({
							url: that.putserve + '/front/flow/sign',
							data: {
								city: city,
								page: 11,
								project: "",
								remark: "登录",
								source: '线上推广2',
								name: "",
								ip: ip,
								position: "106",
								tel: phone,
								kid: kid,
								other: other,
								other: uni.getStorageSync('other'),
								uuid: uni.getStorageSync('uuid')
							},
							method: "GET",
							success: (res) => {
								if (res.data.code == 500) {
									this.msg = res.data.message;
									that.$refs.toast.show()
								} else {
									that.teltxt = phone.substr(0, 3) + "****" + phone.substr(7,
									11);
									let time = 60
									clearInterval(that.timer);
									var fn = function() {
										time--;
										if (time > 0) {
											that.istime = true
											that.timetxt = `已发送(${time}s)`
										} else {
											that.istime = false
											clearInterval(that.timer);
											that.timetxt = `获取验证码`
										}
									};
									fn();
									that.timer = setInterval(fn, 1000);
									if (that.isok == 1) {
										if (!uni.getStorageSync('token')) {
											let openid = uni.getStorageSync('openid')
											uni.request({
												url: "https://api.edefang.net/applets/login",
												method: 'GET',
												data: {
													phone: phone,
													openid: openid,
													other: uni.getStorageSync('other'),
													uuid: uni.getStorageSync('uuid')
												},
												success: (res) => {
													console.log(res)
													uni.setStorageSync('token', res
														.data.token)
													uni.setStorageSync('phone', phone)
												}
											})
										}
										that.msg = "订阅成功";
										that.$refs.toast.show()
									} else {
										that.iscode = true
									}

								}

							}
						})
						uni.request({
							url: that.javaserve + `applets/send/code`,
							method: "POST",
							header: {
								'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
							},
							data: {
								ip: ip,
								phone: phone,
								type: 1,
							},
							success: (res) => {
								console.log(res);
								if (res.data.status == 200) {
									this.msg = '短信已发您手机';
									this.$refs.toast.show();
								} else {
									this.msg = '发送失败，请重试';
									this.$refs.toast.show();
								}
							}
						})
					}
				})


			},
			getLogin() {
				let that = this
				if(!this.ok) {
					return;
				}
				let phone = this.telphone;
				var pattern_phone = /^1[3-9][0-9]{9}$/;
				if (phone == "") {
					return;
				} 
				if (!this.code) {
					return;
				}
				if (!pattern_phone.test(phone)) {
					this.msg = "手机号码不合法"
					this.$refs.toast.show();
					return;
				}
				uni.request({
					url: that.javaserve + '/applets/sure/code',
					method: "POST",
					data: {
						phone: phone,
						type: 1,
						code: that.code,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					success: (res) => {
						console.log(res)
						if (res.data.status === 200) {
							// res.header['page-count']
							console.log(res)
							uni.setStorageSync('phone', phone)
							let basurl = uni.getStorageSync('backurl');
							that.msg = "订阅成功";
							that.$refs.toast.show()
							uni.setStorageSync('token', res.data.data)
							uni.setStorageSync('pass', true)
							if (basurl) {
								uni.removeStorageSync('backurl')
								if (basurl == '/pages/home/home') {
									uni.switchTab({
										url: basurl
									})
								} else {
									uni.navigateTo({
										url: basurl
									})
								}
							} else {
								uni.switchTab({
									url: '/pages/home/home'
								})
							}
						} else {
							that.msg = '验证码不正确';
							that.$refs.toast.show()
						}
					}
				})
			}
		},

	}
</script>

<style lang="less">
	page {
		background: #fff;
	}

	.login {
		.login_box {
			padding-left: 60rpx;
			padding-right: 60rpx;
			box-sizing: border-box;
			width: 100%;

			.title {
				margin-bottom: 37rpx;
				margin-top: 98rpx;

				image {
					width: 392rpx;
					height: 102rpx;
				}
			}

			.tel {
				height: 70rpx;
				width: 590rpx;
				border-bottom: 1rpx solid #EDEDED;
				padding-top: 46rpx;

				input {
					// font-weight: bold;
				}
			}

			.yan {
				height: 70rpx;
				width: 590rpx;
				border-bottom: 1rpx solid #EDEDED;
				padding-top: 46rpx;
				display: flex;
				text {
					font-size: 28rpx;
					font-weight: 500;
					color: #2AC66D;
					margin-left: auto;
				}
			}
			.tishi {
				display: flex;
				align-items: center;
				margin-top: 50rpx;
				.txt {
					color: #9DA3A6;
					font-size: 26rpx;
					text {
						color: #2AC66D;
					}
				}
			}
			.login {
				width: 630rpx;
				height: 96rpx;
				background: #E3E5E6;
				border-radius: 48rpx;
				font-size: 32rpx;
				font-weight: 400;
				color: #FFFFFF;
				text-align: center;
				line-height: 96rpx;
				margin-top: 160rpx;
			}
			.active {
				background: #2AC66D;
			}
		}


	}
</style>
