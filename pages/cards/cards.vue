<template>
	<view>
		<!-- <view class="toptitle" @tap="back">
			<view class="status_bar">
			      </view>
				<image src="../../static/all-back.png" mode=""></image>
				<text>我的卡券</text>
		</view> -->
		<view class="youhui_box" v-if="num>0">
			<view class="youhui_01">
				<text class="text">
					售楼处专供家园平台客户
					<text class="jie">
						（{{deadline}}截止）
					</text>
				</text>
				<view class="right">
					<view class="ling_btn">
						已领取
					</view>
					<!-- <text>{{receive_num_5000}}人已领取</text> -->
				</view>
			</view>
			<view class="youhui_02">
				<text class="text">
					免费专车1对1服务限时券
					<text class="jie">
						（剩余{{remain_num}}张）
					</text>
				</text>
				<view class="right">
					<view class="ling_btn">
						已领取
					</view>
					<!-- <text>{{receive_num_car}}人已领取</text> -->
				</view>
			</view>
		</view>
		
		<view class="isnull" v-else>
			<view class="imgbox">
				<image src="../../static/other/cards-con.png" mode=""></image>
			</view>
			<view class="txt">
				您还没有领取的卡券
			</view>
		</view>
		
		
		
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				num:0,
				receive_num_5000:"",
				receive_num_car:"",
				deadline:"",
				remain_num:""
			}
		},
		onLoad(){
			this.getdata();
			//#ifdef MP-BAIDU
			swan.setPageInfo({
				title: '家园新房-我的卡券',
				keywords: '家园新房-我的卡券',
				description: '家园新房-我的卡券',
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
			back() {
				uni.navigateBack({
					data: 1
				})
			},
			getdata(){
				if(uni.getStorageSync('token')) {
					this.num=2
				}
				let date = new Date(new Date().getTime()+1000 * 60 * 60 * 24*4)
				
				this.deadline = ((date.getMonth()+1)>=10?(date.getMonth()+1):'0'+(date.getMonth()+1))+'-'+(date.getDate()>=10?date.getDate():'0'+date.getDate());
				this.remain_num = Math.floor(Math.random() * (200 - 100)) + 100;
			}
		}
	}
</script>

<style lang="less">
	page{
		background: #FFFFFF;
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
	.isnull {
		.imgbox {
			display: flex;
			justify-content: center;
			image {
				margin-top: 190rpx;
				width: 400rpx;
				height: 400rpx;
			}
		}
		.txt {
			text-align: center;
			color: #7D7E80;
			font-size: 26rpx;
		}
	}
	.youhui_box{
		padding-left: 30rpx;
		padding-right: 30rpx;
		box-sizing: border-box;
		width: 100%;
		background: #fff;
		padding-top: 20rpx;
		.youhui_01 {
			width: 100%;
			height: 140rpx;
			background: url(../../static/content/youhui_01.png) no-repeat;
			background-size: 100% 140rpx;
			margin-bottom: 40rpx;
			margin-top: 2rpx;
			position: relative;
		
			.text {
				font-size: 24rpx;
				font-weight: 400;
				color: #E6813D;
				position: absolute;
				bottom: 22rpx;
				left: 30rpx;
		
				.jie {
					font-size: 20rpx;
					font-weight: 500;
					color: #211C18;
				}
			}
		
			.right {
				.ling_btn {
					width: 150rpx;
					height: 52rpx;
					background: linear-gradient(270deg, #FF7519, #FFAE3D);
					border-radius: 26rpx;
					font-size: 24rpx;
					font-weight: 500;
					color: #FFFFFF;
					text-align: center;
					line-height: 52rpx;
					position: absolute;
					top: 44rpx;
					right: 30rpx;
				}
		
				text {
					font-size: 24rpx;
					font-weight: 500;
					color: #FF7519;
					position: absolute;
					right: 48rpx;
					bottom: 19rpx;
				}
			}
		}
		.youhui_02 {
			width: 100%;
			height: 140rpx;
			background: url(../../static/content/youhui_02.png) no-repeat;
			background-size: 100% 140rpx;
			position: relative;
		
			.text {
				font-size: 24rpx;
				font-weight: 400;
				color: #68938C;
				position: absolute;
				bottom: 22rpx;
				left: 30rpx;
		
				.jie {
					font-size: 20rpx;
					font-weight: 500;
					color: #211C18;
				}
			}
		
			.right {
				.ling_btn {
					width: 150rpx;
					height: 52rpx;
					background: linear-gradient(270deg, #28C567, #81DB85);
					border-radius: 26rpx;
					font-size: 24rpx;
					font-weight: 500;
					color: #FFFFFF;
					text-align: center;
					line-height: 52rpx;
					position: absolute;
					top: 44rpx;
					right: 30rpx;
				}
		
				text {
					font-size: 24rpx;
					font-weight: 500;
					color: #40A2F4;
					position: absolute;
					right: 48rpx;
					bottom: 19rpx;
				}
			}
		}
		
		
		
	}
</style>
