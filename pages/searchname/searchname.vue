<template>
	<view class="searchname">
		<!-- <view class="toptitle" @tap="back">
			<view class="status_bar">
			      </view>
			<image src="../../static/all-back.png" mode=""></image>
			<text>搜索楼盘</text>
		</view> -->
		<view class="box">
			<view class="top-bx">
				<view class="top-input">
					<image src="../../static/other/searchname-icon.png" mode=""></image>
					<input type="text" value="" placeholder="请输入楼盘名称" placeholder-class="istxt" v-model="name" />
				</view>
			</view>
			
			<view class="hots">
				<view class="tit">
					<image src="../../static/other/searchname-hot.png" mode=""></image>
					<text>大家都在搜</text>
				</view>
				<view class="content">
					<view class="itembox" v-for="item in hots" :key="item.id" @tap="go(item.id,item.name)">
						<view class="name">
							{{item.name}}
						</view>
						<view class="icons">
							<view class="icon">
								{{item.type}}
							</view>
							<view class="icon">
								{{item.decorate}}
							</view>
							<view class="icon" v-if="item.railway">
								{{item.railway}}
							</view>
						</view>
						<view class="price">
							{{item.price}}<text>元/m²</text>
						</view>
						<image src="../../static/other/fz.png"></image>
					</view>
				</view>
			</view>
			<view class="hots looks">
				<view class="tit">
					<image src="../../static/other/searchname-eye.png" mode=""></image>
					<text>大家都在看</text>
				</view>
				<view class="content">
					<view class="item" v-for="(item,key) in sees" :key="key" @tap="go(item.id,item.name)">
						<text class="key active">{{key+1}}</text>
						<view class="con">
							<view class="name">
								{{item.name}}
							</view>
							<view class="msg">
								{{item.type}} | {{item.city}}-{{item.country}} | {{item.area}}m²
							</view>
						</view>
						<view class="price">
							{{item.price}}<text>元/m²</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		<view class="upbox" v-if="name">
			<view class="tit">为您找到如下“{{name}}”相关搜索</view>
			<view class="li" v-for="item in lists" :key="item.id" @tap="go(item.id,item.name)">
				<view class="name">
					<view class="left" v-html="item.name"></view>
					<view class="right">{{item.price}}元/m²</view>
				</view>
				<view class="address">
					{{item.country}} - <view v-html="item.address"></view>
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
			this.city = uni.getStorageSync('city');
			this.getinfo()
		},
		data() {
			return {
				name: '',
				hots: [],
				lists: [],
				city: 1,
				sees: []
			}
		},
		methods: {
			back() {
				uni.navigateBack({
					delta: 1
				})
			},
			getinfo() {
				let token = uni.getStorageSync('token')
				let city = this.city
				uni.request({
					url: that.javaserve + '/applets/jy/search/recommends',
					method: 'GET',
					data: {
						city: city,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					success: (res) => {
						console.log(res)
						that.hots = res.data.data.searches
						that.sees = res.data.data.us_see
						//#ifdef MP-BAIDU
						swan.setPageInfo({
							title: '家园新房-楼盘搜索',
							keywords: '家园新房-楼盘搜索',
							description: '家园新房-楼盘搜索',
							success: res => {
								console.log('setPageInfo success', res);
							},
							fail: err => {
								console.log('setPageInfo fail', err);
							}
						})
						//#endif
					}
				})
				uni.request({
					url: that.apiserve + '/jy/us/search',
					method: 'GET',
					data: {
						city: city,
						token: token,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					success: (res) => {
						// that.hots = res.data.hot_search
					}
				})
			},
			go(id, name) {
				let sea = uni.getStorageSync('search')
				if (sea) {
					uni.removeStorageSync('search')
					uni.setStorageSync('searchname', name)
					uni.setStorageSync('searchid', id)
					uni.navigateTo({
						url: "/pages/forward/forward"
					})
					return
				}
				uni.navigateTo({
					url: '/pageA/content/content?id=' + id
				})
			},
			sou() {
				let city = uni.getStorageSync('cityname')
				uni.request({
					url: that.putserve + '/api/project/e_search',
					method: "POST",
					data: {
						name: that.name,
						page: 1,
						limit: 10,
						city: city,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					success: (res) => {
						console.log(res)
						that.lists = res.data.data
					}
				})
			}
		},
		watch: {
			name(val) {
				this.sou()
			}
		}
	}
</script>

<style lang="less">
	page {
		background: #FFFFFF
	}

	.toptitle {
		color: #17181A;
		font-size: 32rpx;
		padding: 0 29.88rpx;
		line-height: 87.64rpx;

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

	.box {
		padding: 0 30rpx;
		.top-bx {
			width: 690rpx;
			padding: 0 30rpx;
			position: fixed;
			z-index: 10;
			top: 0;
			height: 88rpx;
			display: flex;
			align-items: center;
			background-color: #fff;
			left: 0;
			.top-input {
				height: 64rpx;
				border-radius: 32rpx;
				background-color: #F5F5F5;
				display: flex;
				align-items: center;
				width: 100%;
			
				image {
					width: 36rpx;
					height: 36rpx;
					margin-right: 10rpx;
					margin-left: 16rpx;
				}
			
				.istxt {
					color: #969799;
					font-size: 28rpx;
				}
			
				input {
					font-size: 28rpx;
					width: 86%;
				}
			}
		}
		

		.hots {
			margin-top: 124rpx;
			.tit {
				margin-bottom: 38rpx;

				image {
					width: 36rpx;
					height: 36rpx;
					margin-right: 20rpx;
					margin-bottom: -4rpx;
				}

				text {
					color: #19191A;
					font-size: 30rpx;
				}
			}

			.content {
				margin-bottom: 50rpx;
				overflow: hidden;

				.itembox {
					float: left;
					margin-right: 20rpx;
					margin-bottom: 20rpx;
					width: 305rpx;
					height: 180rpx;
					border-radius: 16rpx;
					background: #FFF9F5;
					padding-left: 30rpx;
					position: relative;

					.name {
						font-size: 28rpx;
						font-weight: bold;
						color: #AA6231;
						padding-top: 22rpx;
						margin-bottom: 12rpx;
					}

					.icons {
						margin-bottom: 10rpx;
						display: flex;

						.icon {
							padding: 5rpx 12rpx;
							padding-top: 3rpx;
							border: 1rpx solid #E0BBA3;
							border-radius: 6rpx;
							font-size: 20rpx;
							color: #C27D4F;
							margin-right: 13rpx;
						}
					}

					.price {
						font-size: 34rpx;
						color: #FF7F00;
						font-weight: 800;

						text {
							font-size: 26rpx;
							position: relative;
							top: -2rpx;
						}
					}

					image {
						position: absolute;
						width: 80rpx;
						height: 80rpx;
						right: 0;
						bottom: 0;
					}
				}

				.itembox:nth-of-type(2n) {
					margin-right: 0;
				}

				.item {
					display: flex;
					align-items: center;
					border-bottom: 1rpx solid #F2F2F2;
					padding-bottom: 28rpx;
					margin-bottom: 30rpx;

					.key {
						color: #999999;
						font-size: 32rpx;
						font-weight: bold;
					}

					.active {
						color: #FF7519;
					}

					.con {
						margin-left: 34rpx;

						.name {
							color: #313233;
							font-size: 32rpx;
							font-weight: bold;
							margin-bottom: 20rpx;
						}

						.msg {
							color: #4B4C4D;
							font-size: 24rpx;
						}
					}

					.price {
						color: #FF6040;
						font-size: 32rpx;
						font-weight: 800;
						margin-left: auto;

						text {
							font-size: 26rpx;
						}
					}
				}

				.item:last-child {
					border: 0;
				}
			}
		}
		.looks {
			margin-top: 42rpx;
		}
	}

	.upbox {
		width: 96%;
		padding-left: 4%;
		position: fixed;
		top: 74rpx;
		height: 92vh;
		background-color: #FFFFFF;
		overflow: auto;
		padding-top: 50rpx;
		.tit {
			color: #333436;
			font-size: 28rpx;
			margin-bottom: 58rpx;
		}

		.li {
			border-bottom: 1rpx solid #F2F4F7;
			margin-bottom: 32rpx;

			.name {
				margin-bottom: 24rpx;
				overflow: hidden;

				.left {
					color: #303233;
					font-size: 32rpx;
					font-weight: bold;
					float: left;
				}

				.right {
					color: #FF6040;
					font-size: 26rpx;
					float: right;
					margin-right: 30rpx;
				}
			}

			.address {
				color: #7D7E80;
				font-size: 24rpx;
				display: flex;

				view {
					margin-left: 10rpx;
				}
			}
		}
	}
</style>
