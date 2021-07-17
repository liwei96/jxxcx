<template>
	<view class="about">
		<view class="con">
			<view class="titmsg">
				<view class="small" @tap="test">
					关于我们
				</view>
				<view class="big">
					ABOUT US
				</view>
			</view>
			<view class="item-txt">
				家园新房是杭州亚汉网络科技科技有限公司打造的一手房信息平台，集房源展示、实时咨询、房产动态跟踪、价格对比等功能于一体。
			</view>
			<view class="item-txt">
				www.jy1980.com是我们专属的网站域名，其中“jy”是家园的缩写，“1980”则是为了纪念1980年6月我国开始正式实施住房商品化政策，这两个简写也是家园的初衷，为让更多购房者买到属于自己的家园而努力。
			</view>
			<view class="item-txt">
				家园正在打造一个全面的房产信息库，让购房这件事更透明、更简单。目前平台内汇集了碧桂园，恒大，融创，融信、绿城等众多知名房企开发的优质项目。这些楼盘的近期动态和房价变化也都能够做到第一时间更新，为购房者分享着新鲜、全面的房产资讯。而且这些项目信息全部存放在项目数据库中，通过平台的智能搜索便能快速直达。
			</view>
			<view class="item-txt">
				我们欢迎有想法有资源的人士以任意方式加入我们平台，你可以选择以分站合作运营、媒体合作，开发商直接推广合作等方式加入我们，古人云“海内存知己，天涯若比邻”，希望我们能有这个机会一起努力，共创房产新时代
			</view>
			<view class="item-txt bomtxt">
				公司位于：浙江省杭州市萧山区宁围街道杭州国际办公中心嘉润铭座7幢2单元1303室-1
			</view>
			<view class="item-txt bomtxt">
				联系电话：400-718-6686
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
				title: '家园新房-关于我们',
				keywords: '家园新房-关于我们',
				description: '家园新房-关于我们',
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
			test() {
				uni.chooseImage({
				    success: (chooseImageRes) => {
				        const tempFilePaths = chooseImageRes.tempFilePaths;
						console.log(tempFilePaths)
				        // uni.uploadFile({
				        //     url: 'https://www.example.com/upload', //仅为示例，非真实的接口地址
				        //     filePath: tempFilePaths[0],
				        //     name: 'file',
				        //     formData: {
				        //         'user': 'test'
				        //     },
				        //     success: (uploadFileRes) => {
				        //         console.log(uploadFileRes.data);
				        //     }
				        // });
				    }
				});
			}
		}
	}
</script>

<style lang="less">
	page{
		background: #FFFFFF;
	}
	.about {
		background-color: #FFFFFF;
	}

	.con {
		padding: 0 30rpx;
		margin-bottom: 10rpx;
		padding-bottom: 50rpx;
		.titmsg {
			position: relative;
			margin-bottom: 44rpx;
			margin-top: 68rpx;
			.big {
				position: absolute;
				color: #F2F2F2;
				font-size: 48rpx;
				font-weight: bold;
				left: 50%;
				transform: translateX(-50%);
				top: -18rpx;
			}
		
			.small {
				color: #2A2A2A;
				font-size: 36rpx;
				font-weight: bold;
				margin-bottom: 8rpx;
				text-align: center;
				z-index: 2;
				position: relative;
			}
		
			.small-txt {
				color: #2A2A2A;
				font-size: 18rpx;
				text-align: center;
			}
		}

		.item-txt {
			color: #303233;
			font-size: 28rpx;
			line-height: 46rpx;
			text-indent: 52rpx;
			margin-bottom: 24rpx;
		}
		.bomtxt {
			text-indent: 0;
		}
	}
</style>
