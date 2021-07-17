<template>
	<view>
		<!-- <view class="toptitle" @tap="back">
			<view class="status_bar">
			      </view>
			<image src="../../static/all-back.png" mode=""></image>
			<text>买房百科</text>
		</view> -->
		<view class="tit" @tap="gosearch">
			<image src="../../static/other/weike-search.png" mode=""></image>
			<input type="text" value="" placeholder="搜搜你想要了解的房产知识吧" placeholder-class="txt" disabled/>
		</view>
		<view class="swiper">
			<swiper :autoplay="true" :interval="3000" :duration="1000" :circular="true" previous-margin="60rpx" next-margin="60rpx"
			 @change="setnum">
				<swiper-item v-for="(item,key) in tops" :key="key">
					<view class="swiper-item" @tap="go(item.id)">
						<image :src="item.img" mode=""></image>
						<view class="img-msg">
							{{item.title}}
						</view>
					</view>
				</swiper-item>
			</swiper>
			<view class="swiper-num">
				<view v-for="(item,key) in tops" :key="key" :class="key === currentnum ? 'active' : ''"></view>
			</view>
		</view>

		<view class="swiper-nav">
			<scroll-view class="scroll-nav" scroll-x="true">
				<view  v-for="(item,key) in navs" :key="key" :class="navnum === item.id ? 'swiper-item active' : 'swiper-item'" @click="setnavnum(item.id)">{{item.name}}
				</view>
                </scroll-view>
		</view>
		<view class="lists">
			<view class="other">
				<view class="other-li" v-for="(item,key) in infos" :key="item.id" @tap="go(item.id)">
					<view class="left">
						<view class="li-tit">
							<view v-if="key == 0">新</view>{{item.title}}
						</view>
						<view class="li-icons">
							<text>{{item.source}}</text>
							<text>{{item.time}}</text>
						</view>
					</view>
					<view class="right">
						<image :src="item.img" mode=""></image>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	var that
	export default {
		onLoad(options) {
			that = this
			this.city = options.city || uni.getStorageSync('city')
			this.gettop()
			if(options.infos) {
				this.navnum = options.infos
			}
			this.getdata()
		},
		data() {
			return {
				city: 1,
				isok: true,
				currentnum: 0,
				tops: [
				],
				navs: [{
						id: "46",
						name: "楼盘导购",
					},
					{
						id: "48",
						name: "本地楼市",
					},
					{
						id: "49",
						name: "房企资讯",
					},
					{
						id: "50",
						name: "热点新闻",
					},
					{
						id: "54",
						name: "日报",
					},
					{
						id: "75",
						name: "周报",
					},
					{
						id: "55",
						name: "月报",
					},
					{
						id: "52",
						name: "土拍成交",
					}
				],
				navnum: '46',
				page: 1,
				infos: [],
				total: 0
			}
		},
		onReachBottom(e) {
			this.getmore()
		},
		methods: {
			gosearch() {
				uni.navigateTo({
					url:'/pages/searchinfo/searchinfo'
				})
			},
			back(){
				uni.navigateBack({
					data: 1
				})
			},
			setnum(e) {
				this.currentnum = e.detail.current
			},
			gettop() {
				let city = uni.getStorageSync('city')
				let token = uni.getStorageSync('token')
				uni.request({
					url: that.javaserve + "/applets/jy/articles/head",
					method: "POST",
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					data: {
						city: city,
						limit: 4,
						token: token,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					success: (res) => {
						that.tops = res.data.data.data
					}
				})
			},
			getdata() {
				uni.showLoading({
					title: '加载中'
				})
				let token = uni.getStorageSync('token')
				let city = this.city
				uni.request({
					url: that.javaserve + '/applets/jy/article/info',
					method: 'POST',
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					data: {
						city: city,
						position: that.navnum,
						page: that.page,
						limit: 10,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					success: (res) => {
						console.log(res)
						that.total = res.data.data.total
						that.infos = res.data.data.data
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
						uni.hideLoading()
					}
				})
			},
			getmore() {
				this.page = this.page+1
				uni.showLoading({
					title: '加载中'
				})
				let token = uni.getStorageSync('token')
				let city = uni.getStorageSync('city')
				uni.request({
					url: that.javaserve + '/applets/jy/article/info',
					method: 'POST',
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					data: {
						city: city,
						position: that.navnum,
						page: that.page,
						limit: 10,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					success: (res) => {
						console.log(res)
						that.infos = that.infos.concat(res.data.data.data)
						uni.hideLoading()
					}
				})
			},
			go(id) {
				uni.navigateTo({
					url: "/pages/info/info?id="+id
				})
			},
			setnavnum(id) {
				this.navnum = id
				this.page = 1
				this.getdata()
				// uni.navigateTo({
				// 	url: "/pages/infos/infos?infos="+id
				// })
			}
		}
	}
</script>

<style lang="less">
	page{
		background:#FFFFFF;
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
			width: 32rpx;
			height: 32rpx;
			margin-right: 14rpx;
			margin-bottom: -4rpx;
		}
	}

	.tit {
		width: 92%;
		margin-left: 4%;
		height: 64rpx;
		border-radius: 32rpx;
		background: #F5F5F5;
		display: flex;
		align-items: center;
		margin-top: 12rpx;
		margin-bottom: 40rpx;
		image {
			width: 32rpx;
			height: 32rpx;
			margin-left: 140rpx;
			margin-right: 10rpx;
		}

		.txt {
			color: #969799;
			font-size: 28rpx;
		}

		input {
			font-size: 28rpx;
		}
	}

	.swiper {
		padding-bottom: 44rpx;
		position: relative;

		.swiper-item {
			position: relative;
			border-radius: 12rpx;
			overflow: hidden;
			height: 280rpx;
			margin: 0 15rpx;
			text-align: center;

			image {
				width: 630rpx;
				height: 280rpx;
				border-radius: 12rpx;
			}

			.img-msg {
				position: absolute;
				width: 92%;
				padding: 0 4%;
				height: 52rpx;
				background: rgba(0, 0, 0, 0.4);
				line-height: 52rpx;
				text-align: center;
				overflow: hidden;
				text-overflow: ellipsis;
				white-space: nowrap;
				font-size: 26rpx;
				color: #FFFFFF;
				bottom: 0;
			}
		}

		.swiper-num {
			display: flex;
			justify-content: center;
			position: absolute;
			width: 100%;
			bottom: 42rpx;

			view {
				width: 12rpx;
				height: 4rpx;
				border-radius: 2rpx;
				background: #F0F0F2;
				margin-right: 8rpx;
			}

			.active {
				width: 24rpx;
				height: 4rpx;
				background-color: #20C657;
			}
		}
	}



	.swiper-nav {
		white-space: nowrap;
		height: 60rpx;
		margin-bottom: 25rpx;
		.scroll-nav {
		}
		.swiper-item {
			display: inline-block;
			background-color: #F2F2F2;
			width: 168rpx;
			color: #4A4C4D;
			font-size: 28rpx;
			margin-right: 36rpx;
			height: 64rpx;
			text-align: center;
			margin-right: 24rpx;
			line-height: 64rpx;
			border-radius: 32rpx;
		}
		.swiper-item:nth-of-type(1) {
			margin-left: 30rpx;
		}
		.active {
			color: #2AC66D;
			font-size: 32rpx;
			font-weight: 800;
			background-color: #E6FAEF;
		}
	}

	.other {
		padding: 0 30rpx;
		border-top: 1rpx solid #f7f7f7;
		padding-top: 25rpx;
		.other-li {
			display: flex;
			margin-bottom: 4rpx;

			.left {
				flex: 1;

				.li-tit {
					color: #16181A;
					font-size: 32rpx;
					line-height: 46rpx;
					display: -webkit-box;
					-webkit-box-orient: vertical;
					-webkit-line-clamp: 2;
					overflow: hidden;
					margin-bottom: 14rpx;

					view {
						font-size: 24rpx;
						color: #FFFFFF;
						background-color: #FF4040;
						border-radius: 4rpx;
						width: 30rpx;
						height: 30rpx;
						display: inline-block;
						text-align: center;
						line-height: 30rpx;
						margin-top: -4rpx;
					}
				}

				.li-icons {
					text {
						color: #969899;
						font-size: 22rpx;
						margin-right: 16rpx;
					}
				}
			}

			.right {
				width: 220rpx;
				margin-left: 42rpx;

				image {
					width: 100%;
					height: 156rpx;
					border-radius: 12rpx;
				}
			}
		}
	}
</style>
