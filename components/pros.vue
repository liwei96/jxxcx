<template>
	<view class="recommend-box">
		<view class="item" v-for="item in recommends" :key="item.id" @tap="godetail(item.id)">
			<!-- <navigator :url="`/pageA/content/content?id=${item.id}`" class="nav_to"> -->
	
			<view class="left">
				<image :src="item.img" mode=""></image>
			</view>
			<view class="right">
				<view class="right-name">
					<text class="name">{{item.name}}</text>
					<text class="status" v-if="item.state=='在售'">{{item.state}}</text>
					<text class="status_dai" v-if="item.state=='待售'">{{item.state}}</text>
					<text class="status_kong" v-if="item.state =='售空'">{{item.state}}</text>
				</view>
				<text class="price">{{item.price || item.single_price}}</text>
				<text class="psam">元/m²</text>
				<view class="right-msg">
					{{item.type}} | {{item.city}}-{{item.country}} |
						{{item.area}}m²
				</view>
				<view class="right-icons">
					<text class="jing" v-if="item.decorate">{{item.decorate}}</text>
					<text v-if="item.railway">{{item.railway}}</text>
					<text v-if="item.feature">{{item.feature}}</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		props:['recommends'],
		methods:{
			godetail(id) {
				uni.navigateTo({
					url: `/pageA/content/content?id=${id}`
				})
			},
		}
	}
</script>

<style lang="less" scoped>
	.recommend-box {
		margin-top: 31.87rpx;
		.item {
			//display: flex;
			margin-bottom: 59.76rpx;
			width: 100%;
			display: flex;
		
			.left {
				margin-right: 27.88rpx;
		
				image {
					width: 219.12rpx;
					height: 159.36rpx;
					border-radius: 11.95rpx;
				}
			}
		
			.right {
				flex: 1;
		
				.right-name {
					position: relative;
					top: -5.97rpx
				}
		
				.name {
					color: #323233;
					font-size: 31.87rpx;
					margin-bottom: 11.95rpx;
					font-weight: 800;
				}
		
				.status {
					color: #20C657;
					font-size: 21.91rpx;
					background-color: #E6FAF0;
					width: 67.72rpx;
					height: 35.85rpx;
					border-radius: 5.97rpx;
					text-align: center;
					line-height: 35.85rpx;
					float: right;
					display: block;
				}
		
				.status_dai {
					color: #FA941B;
					font-size: 21.91rpx;
					background-color: #FCF6E8;
					width: 67.72rpx;
					height: 35.85rpx;
					border-radius: 5.97rpx;
					text-align: center;
					line-height: 35.85rpx;
					float: right;
					display: block;
				}
		
				.status_kong {
					color: #FFFFFF;
					font-size: 21.91rpx;
					background-color: #D9D9D9;
					width: 67.72rpx;
					height: 35.85rpx;
					border-radius: 5.97rpx;
					text-align: center;
					line-height: 35.85rpx;
					float: right;
					display: block;
				}
		
		
				.price {
					color: #FF6040;
					font-size: 31.87rpx;
					font-weight: 800;
				}
		
				.psam {
					color: #FF6040;
					font-size: 25.89rpx;
					font-weight: 800;
				}
		
				.right-msg {
					color: #4B4C4D;
					font-size: 23.9rpx;
					margin-bottom: 7.96rpx;
					white-space: nowrap;
				}
		
				.right-icons {
					display: flex;
		
					text {
						padding: 5.97rpx 11.95rpx;
						color: #7D7D80;
						font-size: 21.91rpx;
						background-color: #F5F5F5;
						border-radius: 6rpx;
						margin-right: 11.95rpx;
					}
		
					.jing {
						background-color: #EBF8FF;
						color: #628BB9;
					}
				}
			}
		}
		.item:last-child{
			margin-bottom: 0;
		}
	}
	
	
</style>
