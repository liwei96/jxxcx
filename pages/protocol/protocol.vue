<template>
	<view>
		<!-- <view class="toptitle" @tap="back">
			<view class="status_bar">
			      </view>
			<image src="../../static/all-back.png" mode=""></image>
			<text>免责协议</text>
		</view> -->
		<view class="box">
			<view class="tit">
				家园新房免责协议
			</view>
			<view class="txt">
				家园新房对于本网服务包含的或用户经由或从任何与本网服务有关的途径所获得的任何内容、信息或广告，不声明或保证其正确性或可靠性；并且对于用户经本服务上的广告、展示而购买、取得的任何产品、信息或资料，本网不负保证责任。用户自行承担担使用本服务的风险。本网有权但无义务，改善或更正本服务任何部分之任何疏漏、错误。本网不保证以下事项（包括但不限于）：
			</view>
			<view class="txt">
				<text>
                                （1）楼盘信息免责声明\n
楼盘信息由开发商提供，最终以政府部门登记备案为准，请谨慎核查，如该楼盘信息有误，您可以拨打我们的投诉电话4007186686。
                            </text>
			</view>
			<view class="txt">
				<text>
                                （2）新闻内容免责声明\n
凡本站注明 “来自：XXX(非家园新房)”的新闻稿件和图片作品，系本站转载自其它媒体，转载目的在于信息传递，并不代表本站赞同其观点和对其真实性负责。如有新闻稿件和图片作品的内容、版权以及其它问题的，请联系本站新闻中心。
                            </text>
			</view>
			<view class="txt">
				<text>
                                （3）用户言论免责声明\n
网友问答仅供其表达个人疑惑或见解，并不代表本网立场，对其正确性、合法性、正当性本网不负任何责任
                            </text>
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
		onLoad() {
			//#ifdef MP-BAIDU
			swan.setPageInfo({
				title: '家园新房-免责协议',
				keywords: '家园新房-免责协议',
				description: '家园新房-免责协议',
				success: res => {
					console.log('setPageInfo success', res);
				},
				fail: err => {
					console.log('setPageInfo fail', err);
				}
			})
			//#endif
		},
		methods: {
			back(){
				uni.navigateBack({
					data:1
				})
			}
		}
	}
</script>

<style lang="less">
	page{
		background: #FFFFFF;
	}
	.box {
		padding: 0 30rpx;
	}
	.tit {
		margin-top: 34rpx;
		margin-bottom: 32rpx;
		color: #12181F;
		font-size: 34rpx;
		font-weight: bold;
	}
	.txt {
		color: #303233;
		font-size: 28rpx;
		line-height: 46rpx;
		margin-bottom: 30rpx;
	}
</style>
