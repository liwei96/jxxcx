<template>
	<view class="sign">
		<view class="signbox" v-if="!ishangda">
			<view class="title">{{title}}</view>
			<view class="txt">{{text}}</view>
			<view class="one" v-if="!iscode">
				<view class="input">
					<input type="text" placeholder="请输入手机号" placeholder-class="place" v-model="tel" :disabled="ok" />
					<view class="setnull" v-if="ok" @tap="setnull">
						x
					</view>
				</view>
				<view class="msg">
					<jiuaicheckbox @change='changeBox' borderStyle='1px solid #D4D7D9' color='#2AC66D' :checked='checked'
						:borderRadius='6' :fontSize='20' :checkBoxSize='24'></jiuaicheckbox>我已阅读并同意<text>《家园服务协议》</text>
				</view>
				<view class="btn" @tap="send">
					立即订阅
				</view>
				<view class="bomtxt">
					有专属咨询师为您提供专业的购房意见和1v1服务
				</view>
			</view>
			<view class="two" v-if="iscode">
				<view class="telmsg">
					验证码已发送到{{teltxt}} 请注意查看
				</view>
				<view class="input">
					<input type="text" value="" placeholder="请输入验证码" placeholder-class="place" v-model="code" />
					<text>{{timetxt}}</text>
				</view>
				<view class="btn btn1" @tap="sure">
					确定
				</view>
			</view>
		</view>
		<view class="hd" v-if="ishangda">
			<view class="imgbox">
				<image src="../pageA/static/special/hengda.jpg" mode=""></image>
			</view>
			<view class="bom">
				<view class="inputbox">
					<input type="text" value="" placeholder="输入身份证号后6位"/>
				</view>
				<view class="zhu">
					注: 根据本楼盘售楼处规定，实地看房需先提前报备身份证后6位
				</view>
				<view class="bb">
					申请报备
				</view>
			</view>
		</view>
		<toast ref="toast" :txt="toasttxt"></toast>
	</view>
</template>
<script>
	import jiuaicheckbox from '@/components/jiuai-checkbox/jiuai-checkbox.vue'
	import toast from '@/components/mytoast/mytoast.vue'
	export default {
		props: {
			title: {
				type: String
			},
			type: {
				type: Number
			},
			pid: {
				type: Number
			},
			name: {
				type: String
			},
			remark: {
				type: String
			},
			position: {
				type: Number
			},
			isok: {
				type: Number
			}
		},
		data() {
			return {
				ishangda: false,
				tel: '',
				iscode: false,
				second: 60,
				toasttxt: '',
				checked: true,
				teltxt: '',
				timetxt: '',
				istime: false,
				text: '',
				ok: true,
				weixin: false,
				interval: {}
			}
		},
		components: {
			jiuaicheckbox,
			toast
		},
		methods: {
			setnull() {
				this.tel = ''
				this.ok = false
			},
			changeBox(e) { //选中切换事件(由组件发起)
				console.log('点击了checkBox', e);
				this.checked = e.detail.checked
			},
			send() {
				let that = this;
				let checks = this.checked;
				if (!checks) {
					this.toasttxt = "请勾选家园服务协议";
					this.$refs.toast.show()
					return;
				}
				var phone = this.tel;
				var pattern_phone = /^1[3-9][0-9]{9}$/;
				if (phone == "") {
					this.toasttxt = "请输入手机号";
					this.$refs.toast.show()
					return;
				} else if (!pattern_phone.test(phone)) {
					this.toasttxt = "请输入正确手机号";
					this.$refs.toast.show()
					return;
				}
				let kid = uni.getStorageSync('kid') || null
				let other = uni.getStorageSync('other') || null
				let ip = ''
				let city = uni.getStorageSync('city') || 1
				uni.request({
					url: that.putserve + '/getIp.php',
					method: 'GET',
					success: (res) => {
						ip = res.data
						ip = ip.split('=')[1].split(':')[1]
						ip = ip.substring(1)
						ip = ip.slice(0, -3)
						
						uni.request({
							url: that.putserve + '/front/flow/sign',
							data: {
								city: city,
								page: 11,
								project: that.pid,
								remark: that.remark,
								source: '线上推广1',
								name: that.name,
								ip: ip,
								position: that.position,
								tel: phone,
								kid: kid,
								other: uni.getStorageSync('other'),
								uuid: uni.getStorageSync('uuid'),
								site: 1,
								device: 3
							},
							method: "GET",
							success: (res) => {
								if (res.data.code == 500) {
									that.toasttxt = res.data.message;
									that.$refs.toast.show()
									setTimeout(() => {
										that.$emit('closethis', false)
									}, 2000)
								} else {
									that.teltxt = phone.substr(0, 3) + "****" + phone.substr(7,
									11);
									let time = 60
									let fn = function() {
										time--;
										if (time > 0) {
											that.istime = true
											that.timetxt = `${time}秒后重发`
										} else {
											that.istime = false
											clearInterval(that.interval);
											that.timetxt = `获取验证码`
										}
									};
									fn();
									that.interval = setInterval(fn, 1000);
									if (that.isok == 1 && that.ok) {
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
										that.toasttxt = "订阅成功";
										that.$refs.toast.show()
										setTimeout(() => {
											that.$emit('closethis', false)
										}, 2000)
									} else {
										that.iscode = true
										that.$emit('changetype', 1)
									}
								}
							}
						})
						if (that.isok == 1 && that.ok) {
							return
						}
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
							}
						})
					}
				})
			},
			sure() {
				let that = this
				if (!this.code) {
					this.toasttxt = "请输入验证码";
					this.$refs.toast.show()
					return;
				}
				var phone = this.tel;
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
							that.toasttxt = "订阅成功";
							that.$refs.toast.show()
							setTimeout(() => {
								that.$emit('closethis', true)
							}, 2000)
							if (!uni.getStorageSync('token')) {
								uni.setStorageSync('token', res.data.token)
								uni.setStorageSync('phone', that.tel)
								uni.setStorageSync('pass', true)
							}
						} else {
							that.toasttxt = '验证码不正确';
							that.$refs.toast.show()
						}
					}
				})
			},
			setmag(type) {
				if (type == "变价通知我") {
					this.text =
						"价格变动这么快？订阅楼盘变价通知，楼盘变价我们将第一时间通知您";
				} else if (type == "开盘提醒我") {
					this.text = "一键订阅最新开盘通知，我们会第一时间通知,不再错过开盘时间";
				} else if (type == "预约看房") {
					this.text = "提前预约看房，我们将提供免费专车接送和专业楼盘讲解";
				} else if (type == "订阅实时动态") {
					this.text = "订阅最新动态，楼盘最新情报抢先知道，帮您找准买房好时机";
				} else if (type == "获取详细周边配套") {
					this.text = "想了解更多周边配套信息？立即获取全面周边配套详解";
				} else if (type == "获取最新成交价") {
					this.text = "获取最新成交价格，看看房友都是什么价格买的房";
				} else if (type == "咨询户型底价") {
					this.text = "好楼盘户型是关键，咨询详细户型信息，安安心心买房";
				} else if (type == "领取分析资料") {
					this.text = "最新楼盘分析资料，看看房产专家对楼盘的投资分析和宜居分析解读";
				} else if (type == "咨询特价房") {
					this.text = "一键预约看房免费小车上门接送，可带家人一起参观多个热门楼盘";
				} else if (type == "领取优惠") {
					this.text = "专享限时优惠折扣，家园专场推出，早抢早优惠";
				} else if (type == "免费领取") {
					this.text = "精准匹配房源，免费接送一次看完好房";
				} else if (type == "获取详细分析报告") {
					this.text = "向允家咨询师免费领取分析报告,内附有购房流程全盘解读";
				} else if (type == "咨询楼盘底价") {
					this.text = "好楼盘户型是关键，咨询户型底价，安安心心买房";
				} else if (type == "咨询服务") {
					this.text = "立即报名，专业人员为你解惑!";
				} else if (type == "领取分析资料") {
					this.text = "最新楼盘分析资料，看看房产专家对楼盘的投资分析和宜居分析解读";
				} else if (type == "一键咨询") {
					this.text = "立即报名，专业人员为你解惑!";
				} else if (type == "免费咨询") {
					this.text = "立即报名，专业人员为你解惑!";
				} else if (type == "咨询详细楼盘信息") {
					this.text = "向家园咨询师免费领取楼盘资料,内附有购房流程全盘解读";
				} else if (type == "免费专车看房") {
					this.text = "免费专车看房，楼下接您随时出发，可带家人一起看楼盘";
				} else if (type == "查低价") {
					this.text = "免费专车看房，楼下接您随时出发，可带家人一起看楼盘";
				} else if (type == "领取全部户型资料") {
					this.text = "好楼盘户型是关键，咨询户型底价，安安心心买房";
				} else if (type == "贷款方案咨询") {
					this.text = "咨询家园咨询师，了解详细的贷款方案，给你买房最好方案";
				} else if (type == "") {
					this.text = "咨询家园咨询师，了解详细的贷款方案，给你买房最好方案";
				}
			}
		},
		onShow() {
			let type = this.title
			console.log(type)
		},
		mounted() {
			console.log(this.isok)
			if (this.type == 0) {
				this.iscode = false
			}
			if (this.isok == 1) {
				this.ok = true
			} else {
				this.ok = false
			}
			if (this.isok == 1 || this.weixin) {
				this.tel = uni.getStorageSync('phone')
			} else {
				this.tel = ''
			}
			let type = this.title
			console.log(type)
			this.setmag(type)
		},
		watch: {
			type(val) {
				console.log(val)
				if (val == 0) {
					this.iscode = false
				}
			},
			isok(val) {
				console.log(val)
				if (val == 1) {
					this.tel = uni.getStorageSync('phone')
					console.log(this.tel)
					this.ok = true
				} else {
					this.tel = ''
					this.ok = false
				}
			},
			title(val) {
				console.log(val)
				let type = val
				this.setmag(type)
			}
		},
		beforeDestroy() {
			clearInterval(this.interval);
		}
	}
</script>
<style lang="less" scoped>
	.signbox {
		padding: 0 36rpx;
	}

	.title {
		color: #1A1A1A;
		font-size: 44rpx;
		text-align: center;
		font-weight: bold;
		padding-top: 70rpx;
		margin-bottom: 40rpx;
	}

	.txt {
		color: #1A1A1A;
		font-size: 32rpx;
		line-height: 44rpx;
		margin-bottom: 58rpx;
	}

	.input {
		width: 570rpx;
		height: 108rpx;
		border-radius: 12rpx;
		border: 3rpx solid #D2D7D9;
		display: flex;
		align-items: center;
		margin-bottom: 20rpx;
		position: relative;

		input {
			margin-left: 32rpx;
			font-size: 32rpx;
			width: 92%;
		}

		.setnull {
			position: absolute;
			font-size: 40rpx;
			right: 20rpx;
			top: 24rpx;
			z-index: 100;
		}

		.place {
			color: #999999;
			font-size: 32rpx;
		}

		text {
			color: #7495BD;
			font-size: 32rpx;
			position: absolute;
			top: 36rpx;
			right: 36rpx;
		}
	}

	.msg {
		color: #9DA3A6;
		font-size: 24rpx;
		margin-bottom: 64rpx;

		text {
			color: #7495BD;
		}
	}

	.btn {
		width: 100%;
		height: 88rpx;
		border-radius: 12rpx;
		text-align: center;
		line-height: 88rpx;
		background-color: #2AC66D;
		color: #FFFFFF;
		font-size: 32rpx;
		margin-bottom: 20rpx;
	}

	.btn1 {
		margin-top: 86rpx;
	}

	.bomtxt {
		color: #9DA3A6;
		font-size: 24rpx;
	}

	.telmsg {
		color: #9DA3A6;
		font-size: 24rpx;
		margin-bottom: 24rpx;
	}
	.hd {
		.imgbox {
			margin-bottom: 74rpx;
			image {
				width: 650rpx;
				height: 270rpx;
				border-radius: 16rpx 16rpx 0 0;
			}
		}
		.bom {
			padding: 0 54rpx;
			.inputbox {
				height: 113rpx;
				border: 3rpx solid #D2D7D9;
				line-height: 113rpx;
				padding-left: 34rpx;
				font-size: 32rpx;
				margin-bottom: 22rpx;
				border-radius: 12rpx;
				input {
					line-height: 113rpx;
					height: 113rpx;
				}
			}
			.zhu {
				color: #FF3333;
				font-size: 24rpx;
				line-height: 33rpx;
				margin-bottom: 50rpx;
			}
			.bb {
				width: 100%;
				height: 88rpx;
				border-radius: 12rpx;
				text-align: center;
				line-height: 88rpx;
				background-color: #2AC66D;
				color: #FFFFFF;
				font-size: 30rpx;
				font-weight: bold;
			}
		}
	}
</style>
