<template>
	<view>
		<!-- <view class="toptitle" @tap="back">
			<view class="status_bar">
			      </view>
			<image src="../../static/all-back.png" mode=""></image>
			<text>我的收藏</text>
		</view> -->
		<view class="recommend" v-if="!isnull">
			<pros :recommends="list"></pros>
		</view>
		<view class="isnull" v-if="isnull">
			<image src="../../static/other/collect-con.png" mode=""></image>
			<text>您暂无收藏记录，快去逛逛吧</text>
			<view class="btn" @tap="gosearch">
				去收藏
			</view>
		</view>
		<view class="guess" v-if="isnull">
			<view class="recommend">
				<view class="tit">
					猜你喜欢
				</view>
				<pros :recommends="list"></pros>
			</view>
		</view>
	</view>
</template>

<script>
	var that
	import pros from '../../components/pros.vue'
	export default {
		components:{
			pros
		},
		data() {
			return {
				list: [],
				isnull: false,
				type: true
			}
		},
		onLoad() {
			that = this
			this.getinfo()
			//#ifdef MP-BAIDU
			swan.setPageInfo({
				title: '家园新房-我的收藏',
				keywords: '家园新房-我的收藏',
				description: '家园新房-我的收藏',
				success: res => {
					console.log('setPageInfo success', res);
				},
				fail: err => {
					console.log('setPageInfo fail', err);
				}
			})
			//#endif
		},
		onReachBottom() {
			this.getmore()
		},
		methods: {
			back(){
				uni.navigateBack({
					data:1
				})
			},
			go(id){
				uni.navigateTo({
					url:"/pages/content/content?id="+id
				})
			},
			getmore() {
				if(!this.type){
					return
				}
				this.type = false
				uni.showLoading({
					title:'加载中'
				})
				let token = uni.getStorageSync('token')
				uni.request({
					url:that.javaserve+'/applets/jy/mine/collects',
					method:'POST',
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					data:{
						token: token,
						page: 1,
						limit: 10,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					success: (res) => {
						console.log(res)
						that.list = that.list.concat(res.data.data.data)
						that.type = true
						uni.hideLoading()
					}
				})
			},
			getinfo(){
				uni.showLoading({
					title:'加载中'
				})
				let token = uni.getStorageSync('token')
				uni.request({
					url:that.javaserve+'/applets/jy/mine/collects',
					method:'POST',
					header: {
						'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
					},
					data:{
						token: token,
						page: 1,
						limit: 10,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					success: (res) => {
						console.log(res)
						that.list = res.data.data.data
						if(res.data.data.total == 0) {
							that.isnull = true
						}
						uni.hideLoading()
					}
				})
				// uni.request({
				// 	url:that.apiserve+'/jy/mine/collect',
				// 	method:'GET',
				// 	data:{
				// 		token: token,
				// 		page: 1,
				// 		limit: 10,
				// 		other: uni.getStorageSync('other'),
				// 		uuid: uni.getStorageSync('uuid')
				// 	},
				// 	success: (res) => {
				// 		console.log(res)
				// 		that.list = res.data.data
				// 		if(res.data.total == 0) {
				// 			that.isnull = true
				// 		}
				// 		uni.hideLoading()
				// 	}
				// })
			},
			gosearch(){
				uni.navigateTo({
					url:"/pages/building/building"
				})
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
		font-size: 29.88rpx;
		padding: 0 29.88rpx;
		line-height: 88rpx;
		position: fixed;
		width: 100%;
		top: 0;
		z-index: 9999;
		font-weight: bold;
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
	.recommend {
		padding: 0 30rpx;
		// padding-top: 88rpx;
	
		.tit {
			color: #17181A;
			font-size: 33.86rpx;
			margin-bottom: 31.87rpx;
			font-weight: bold;
		}
	
		.recommend-box {
			margin-top: 31.87rpx;
		}
	}
	.isnull {
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;
		margin-bottom: 68rpx;
		padding-top: 128rpx;
		image {
			width: 200rpx;
			height: 200rpx;
			margin-top: 100rpx;
		}
		text {
			color: #7D7E80;
			font-size: 26rpx;
			margin-bottom: 48rpx;
			margin-top: 36rpx;
		}
		.btn {
			width: 320rpx;
			height: 72rpx;
			border-radius: 36rpx;
			background-color: #2AC66D;
			text-align: center;
			line-height: 72rpx;
			color: #FFFFFF;
			font-size: 28rpx;
		}
	}
</style>
