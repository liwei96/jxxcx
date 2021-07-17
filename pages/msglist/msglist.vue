<template>
	<view class="msglist">
		<view class="li" @tap="gotalk">
			<image src="../../static/other/talkpeo.png" mode=""></image>
			<view class="msg">
				<view class="tit">
					家园分析师
					<text>11:24</text>
				</view>
				<view class="txt">
					为客户提供专业的购房建议
				</view>
			</view>
		</view>
		<view class="li" @tap="gozixun">
			<image src="../../static/other/talkinfo.png" mode=""></image>
			<view class="msg">
				<view class="tit">
					资讯
					<text>11:24</text>
				</view>
				<view class="txt">
					东投悦荣府4月3日即将首开
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			gotalk() {
				uni.navigateTo({
					url: '/pages/message/message'
				})
			},
			gozixun() {
				uni.navigateTo({
					url: '/pageA/newinfo/newinfo'
				})
			}
		}
	}
</script>

<style lang="less" scoped>
	.msglist {
		padding: 0 30rpx;
		padding-top: 38rpx;
		min-height: 100vh;
		background-color: #fff;
		.li {
			display: flex;
			margin-bottom: 36rpx;
			image {
				width: 96rpx;
				height: 96rpx;
				margin-right: 28rpx;
			}
			.msg {
				padding-bottom: 38rpx;
				border-bottom: 1rpx solid #F2F2F2;
				flex: 1;
				.tit {
					color: #19191A;
					font-size: 32rpx;
					display: flex;
					align-items: center;
					margin-bottom: 20rpx;
					text {
						margin-left: auto;
						color: #AFAFB3;
						font-size: 24rpx;
					}
				}
				.txt {
					color: #7D7D80;
					font-size: 26rpx;
				}
			}
		}
	}
</style>
