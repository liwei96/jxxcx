<template>
	<view class="articles">
		<view class="other-tit">
			{{tit}}
		</view>
		<view class="other-li" v-for="item in others" :key="item.id" @tap="go(item.id)">
			<view class="left">
				<view class="li-tit">
					{{item.title}}
				</view>
				<view class="li-icons">
					<text v-for="(val,key) in item.tags" :key="key">{{val}}</text>
				</view>
			</view>
			<view class="right">
				<image :src="item.img" mode=""></image>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		props: ['others','tit'],
		data() {
			return {
				
			}
		},
		methods: {
			go(id) {
				uni.redirectTo({
					url: "/pages/article/article?id=" + id
				})
			},
		}
	}
</script>

<style lang="less" scoped>
.other-tit {
			color: #16181A;
			font-size: 36rpx;
			font-weight: bold;
			margin-bottom: 48rpx;
		}

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
					}

					.li-icons {
						text {
							background-color: #F5F5F5;
							color: #7D7F80;
							font-size: 20rpx;
							margin-right: 12rpx;
							padding: 6rpx 12rpx;
							border-radius: 6rpx;
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
</style>
