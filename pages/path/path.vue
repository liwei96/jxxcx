<template>
	<view class="path" v-if="issure">
		<!-- <view class="toptitle" @tap="back">
			<view class="status_bar">
			</view>
			<image src="../../static/all-back.png" mode=""></image>
			<text>选择城市</text>
		</view> -->
		<view class="nowcity">
			<image src="../../static/other/path-name.png" mode=""></image>
			<text class="now-name">{{name}}</text>
			<text class="msg">当前城市</text>
		</view>
		<view class="hots">
			<text class="tit">热门城市</text>
			<view class="hots-con">
				<view class="item" v-for="item in hots" :key="item.id" @tap="setcity(item.id,item.name)">
					{{item.name}}
				</view>
			</view>
			<view class="allmsg">
				全部城市
			</view>
		</view>
		<view v-for="(item,key) in list" :key="key">
			<view class="line" :id="item" >
				{{item}}
			</view>
			<view class="box">
				<view class="cityitem" v-for="(val,index) in lists[item]" :key="val.id" @tap="gocity(val.id,val.name,index)">
					{{val.name}}
				</view>
			</view>
		</view>
		<view class="right-list">
			<view class="li" @click="to(item)" v-for="(item,key) in list" :key="key">
				{{item}}
			</view>
		</view>
		<wyb-popup ref="popup" type="center" height="650" width="550" radius="8" :showCloseIcon="true" closeIcon="../../static/close.png" closeIconSize="32">
			<view class="bbox">
				<image class="bbimg" src="../../static/path-null.jpg" mode=""></image>
				<view class="tit">
					没有该城市信息
				</view>
				<view class="text">
					非常遗憾的通知您，您当前选择的城市我们并未涉及
				</view>
				<view class="line">
				</view>
				<view class="btn" @tap="showff">
					申请开放
				</view>
				<view class="txt" v-if="show">
					我们已收到您诚挚的申请，在下一个开放城市中我们会优先开放—{{bname}}
				</view>
			</view>
		</wyb-popup>
	</view>
</template>

<script>
	import wybPopup from '@/components/wyb-popup/wyb-popup.vue'
	var that
	export default {
		components: {
			wybPopup
		},
		onLoad() {
			that = this
			this.getinfo()
			this.name = uni.getStorageSync('cityname')
			//#ifdef MP-BAIDU
			swan.setPageInfo({
				title: '家园新房-城市选择',
				keywords: '家园新房-城市选择',
				description: '家园新房-城市选择',
				success: res => {
					console.log('setPageInfo success', res);
				},
				fail: err => {
					console.log('setPageInfo fail', err);
				}
			})
			//#endif
		},
		data() {
			return {
				list: ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V',
					'W', 'X', 'Y', 'Z'
				],
				toView: 'A',
				hots: [],
				lists: [],
				name: '',
				bname: '',
				show:false,
				issure: false
			};
		},
		methods: {
			showdd(name) {
				this.bname = name
				this.$refs.popup.show()
			},
			showff() {
				this.show = true
				let that = this
				setTimeout(()=>{
					that.show = false
					that.bname = ''
					that.$refs.popup.hide()
					uni.navigateTo({
						url:'/pages/alliance/alliance'
					})
				},2000)
			},
			back() {
				uni.navigateBack({
					data: 1
				})
			},
			to(id) {
				uni.createSelectorQuery().select('#' + id).boundingClientRect(data => { //目标位置的节点：类或者id
					uni.createSelectorQuery().select(".path").boundingClientRect(res => { //最外层盒子的节点：类或者id
						uni.pageScrollTo({
							duration: 100, //过渡时间
							scrollTop: data.top - res.top, //返回顶部的top值
						})
					}).exec()
				}).exec();
			},
			getinfo() {
				uni.showLoading({
					title: "加载中"
				})
				let city = uni.getStorageSync('city')
				uni.request({
					url: that.javaserve + "/cities/all",
					method: "GET",
					data:{
						city: city
					},
					success: (res) => {
						console.log(res)
						that.hots = res.data.data.hots
						that.lists = res.data.data.cities
						uni.hideLoading()
						that.issure = true
					}
				})
			},
			setcity(id, name) {
				uni.setStorageSync('city', id)
				uni.setStorageSync('cityname', name)
				uni.reLaunch({
					url: "/pages/index/index?city="+id
				})
			},
			gocity(id, name,k) {
				let type = false
				for (let item of this.hots) {
					if (id == item.id) {
						this.setcity(id, name)
					}else {
						this.showdd(name[k].name)
					}
				}
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
		background-color: #FFFFFF;
		top: 0;
		z-index: 9999;

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
	
	.nowcity {
		height: 98rpx;
		padding: 0 30rpx;
		border-bottom: 1rpx solid #F2F3F7;
		line-height: 98rpx;
		margin-bottom: 30rpx;
		// // padding-top: 88rpx;
		// margin-top: var(--status-bar-height);

		image {
			width: 32rpx;
			height: 32rpx;
			margin-right: 6rpx;
			margin-bottom: -4rpx;
		}

		.now-name {
			color: #121212;
			font-size: 32rpx;
			margin-right: 18rpx;
		}

		.msg {
			color: #969799;
			font-size: 24rpx;
		}
	}

	.hots {
		padding: 0 30rpx;

		.tit {
			color: #121212;
			font-size: 30rpx;
			font-weight: bold;
		}

		.hots-con {
			margin-top: 30rpx;
			overflow: hidden;
			margin-bottom: 20rpx;

			.item {
				width: 144rpx;
				height: 52rpx;
				border-radius: 12rpx;
				text-align: center;
				line-height: 52rpx;
				margin-right: 20rpx;
				margin-bottom: 26rpx;
				float: left;
				background-color: #F2F2F2;
				color: #4B4C4D;
				font-size: 24rpx;
			}

			.item:nth-of-type(4n) {
				margin-right: 0;
			}
		}

		.allmsg {
			color: #303233;
			font-size: 30rpx;
			margin-bottom: 32rpx;
		}
	}

	.line {
		height: 60rpx;
		line-height: 60rpx;
		background-color: #F5F5F5;
		color: #323233;
		font-size: 36rpx;
		font-weight: bold;
		padding-left: 44rpx;
	}

	.box {
		padding: 0 30rpx;

		.cityitem {
			height: 95rpx;
			line-height: 95rpx;
			border-bottom: 1rpx solid #F2F3F7;
			color: #323233;
			font-size: 28rpx;
		}
	}

	.right-list {
		position: fixed;
		right: 28rpx;
		top: 50%;
		transform: translateY(-50%);

		.li {
			color: #646566;
			font-size: 20rpx;
			margin-bottom: 15rpx;
		}
	}
	
	.bbox {
		position: relative;
		width: 100%;
		height: 100%;
		.bbimg {
			width: 100%;
			height: 261rpx;
			border-radius: 20rpx;
		}
		.tit {
			color: #16181A;
			font-size: 36rpx;
			text-align: center;
			margin-top: 20rpx;
			margin-bottom: 34rpx;
		}
		.text {
			padding: 0 38rpx;
			color: #626566;
			font-size: 30rpx;
			line-height: 44rpx;
		}
		.line {
			width: 408rpx;
			height: 1rpx;
			background-color: #EDEDED;
			margin-top: 40rpx;
			margin-left: 40rpx;
		}
		.btn {
			width: 300rpx;
			height: 72rpx;
			border-radius: 36rpx;
			background: #2AC66D;
			box-shadow: 0px 8rpx 16rpx 2rpx rgba(42, 198, 109, 0.16);
			text-align: center;
			line-height: 72rpx;
			color: #FFFFFF;
			font-size: 26rpx;
			position: absolute;
			left: 50%;
			margin-left: -150rpx;
			bottom: 42rpx;
		}
		.txt {
			width: 260rpx;
			height: 160rpx;
			background: rgba(0,0,0,0.8);
			padding: 40rpx;
			color: #DADADA;
			font-size: 28rpx;
			line-height: 40rpx;
			position: absolute;
			z-index: 55;
			left: 50%;
			transform: translateX(-50%);
			margin-left: -160rpx;
			top: 50%;
			transform: translateY(-50%);
			border-radius: 24rpx;
		}
	}
</style>
