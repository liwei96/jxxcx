<template>
	<view class="newinfo">
		<view class="li" v-for="item in list" :key="item.id">
			<view class="tit" @tap="goinfo(item.id)">
				{{item.title}}
			</view>
			<image class="bigimg" :src="item.img" mode="" @tap="goinfo(item.id)"></image>
			<view class="txt" @tap="gobuild">
				【{{item.card.country}}】{{item.card.railway}}、{{item.card.feature}} &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;速看>>
			</view>
			<view class="time">
				<text class="left">{{item.time}}</text>
				<view class="right" @tap="gobuild(item.card.id)">楼盘详情<image src="../static/special/special-back.png" mode=""></image>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		onLoad() {
			this.getinfo()
		},
		onReachBottom() {
			this.getmore()
		},
		data() {
			return {
				list: [],
				page: 2
			}
		},
		methods: {
			getinfo() {
				let city = uni.getStorageSync('city')
				uni.request({
					url: this.javaserve + '/applets/jy/city/buildings/articles',
					method: "GET",
					data: {
						city: city,
						page: 1,
						limit: 10
					},
					success: (res) => {
						console.log(res)
						this.list = res.data.data.data
						
					}
				})
			},
			getmore() {
				let city = uni.getStorageSync('city')
				uni.request({
					url: this.javaserve + '/applets/jy/city/buildings/articles',
					method: "GET",
					data: {
						city: city,
						page: this.page,
						limit: 10
					},
					success: (res) => {
						console.log(res)
						this.list = this.list.concat(res.data.data.data)
						this.page++
					}
				})
			},
			goinfo(id) {
				uni.navigateTo({
					url: "/pages/info/info?id=" + id
				})
			},
			gobuild(id) {
				uni.navigateTo({
					url: "/pageA/content/content?id=" + id
				})
			}
		}
	}
</script>

<style lang="less" scoped>
	.newinfo {
		padding: 0 40rpx;
		background: #fff;
		.li {
			padding-bottom: 36rpx;
			border-bottom: 1rpx solid #F2F2F2;

			.tit {
				color: #12181F;
				font-size: 32rpx;
				line-height: 46rpx;
				font-weight: bold;
				margin-bottom: 26rpx;
				padding-top: 36rpx;
			}

			.bigimg {
				width: 670rpx;
				height: 320rpx;
				border-radius: 12rpx;
				margin-bottom: 20rpx;
			}

			.txt {
				color: #303233;
				font-size: 28rpx;
				margin-bottom: 22rpx;
			}

			.time {
				display: flex;

				.left {
					color: #969899;
					font-size: 24rpx;
				}

				.right {
					color: #2AC66D;
					font-size: 26rpx;
					margin-left: auto;

					image {
						width: 24rpx;
						height: 24rpx;
						margin-left: 4rpx;
					}
				}
			}
		}
	}
</style>
