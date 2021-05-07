<template>
	<view class="content" v-if="isover">
		<!-- <view class="toptitle">
			<view class="status_bar">
			</view>
			<text>允家新房</text>
		</view> -->
		<view class="search">
			<view class="searchbox">
				<text class="cityname" @tap="gopath">{{cityname}}</text>
				<image src="../../static/search/search-down.png" mode="" class="search-path" @tap="gopath"></image>
				<view class="line">
				</view>
				<view class="search-right" @tap="goSearch">
					<image src="../../static/index/index-search.png" mode="" class="right-icon"></image>
					<view class="text">
						请输入楼盘名
					</view>
				</view>
			</view>
		</view>
		<view class="searchpros">
			<view class="prozhao" @tap="gobuild">
				<image src="../../static/index/index-down.png" mode=""></image>
			</view>
			<scroll-view class="scrollpro" scroll-x="true">
				<view class="proname">
					华发·峰荟
				</view>
				<view class="proname">
					华发·峰荟
				</view>
				<view class="proname">
					华发·峰荟
				</view>
				<view class="proname">
					华发·峰荟
				</view>
				<view class="proname">
					华发·峰荟
				</view>
			</scroll-view>
		</view>
		<image src="../../static/index/index-banner.jpg" mode="" class="banner"></image>
		<view class="topbj">
		</view>
		<view class="navs">
			<view class="navsbox">
				<view class="li" @tap="gobuild">
					<image src="../../static/index/index-new.png" mode=""></image>
					<text>新房</text>
				</view>
				<view class="li">
					<image src="../../static/index/index-ditie.png" mode=""></image>
					<text>地铁房</text>
				</view>
				<view class="li" v-if="!isweixin" @tap="gospecial">
					<image src="../../static/index/index-tejia.png" mode=""></image>
					<text>特价房</text>
				</view>
				<view class="li" @tap="gomap">
					<image src="../../static/index/index-map.png" mode=""></image>
					<text>地图找房</text>
				</view>
				<view class="li" @tap="gojoin">
					<image src="../../static/index/index-join.png" mode=""></image>
					<text>平台合作</text>
				</view>
			</view>
			<view class="navsbox">
				<view class="li" @tap="gohelp">
					<image src="../../static/index/index-help.png" mode=""></image>
					<text>帮我找房</text>
				</view>
				<view class="li" @tap="goweike">
					<image src="../../static/index/index-weiki.png" mode=""></image>
					<text>百科</text>
				</view>
				<view class="li" @tap="goinfos">
					<image src="../../static/index/index-zixun.png" mode=""></image>
					<text>资讯</text>
				</view>
				<view class="li" @tap="godynamic">
					<image src="../../static/index/index-dynamic.png" mode=""></image>
					<text>动态</text>
				</view>
				<view class="li">
					<image src="../../static/index/index-question.png" mode=""></image>
					<text>楼盘问答</text>
				</view>
			</view>
			<view class="topnew">
				<view class="topnew-box">
					<image class="icon" src="../../static/index/index-topnew.png" mode=""></image>
					<swiper class="swiper" :vertical="true" :circular="true" :autoplay="true" interval="2000">
						<swiper-item class="swiper-item" v-for="item in tops" :key="item.id">
							<navigator :url="`../article/article?id=${item.id}`">
								<view class="swiper-item uni-bg-red">{{item.title}}</view>
							</navigator>
						</swiper-item>
					</swiper>
				</view>
			</view>
		</view>
		
		<view class="help">
			<image src="../../static/index/index-help.jpg" mode=""></image>
			<text class="help-btn" @tap="bangZhao">帮我找房</text>
		</view>
		<view class="feature">
			<view class="feature-tit">今日特价房<view class="more" @tap="gobuild">更多楼盘<image src="../../static/content/right.png" mode=""></image></view></view>
			<scroll-view class="scroll-view" scroll-x="true">
				<view class="scroll-item" @tap="ganglou(3,'刚需')">
					<view class="item-top">
						<view class="red">
							临安
						</view>
						<view class="name">
							赞成.国潮商务...
						</view>
						<image src="../../static/other/about-logo.jpg" mode=""></image>
					</view>
					<view class="item-bom">
						<view class="old">
							原价: 260万
						</view>
						<view class="new">
							<view class="newleft">
								<image src="../../static/index/index-hot.png" mode=""></image>
								特价
							</view>
							<view class="newright">
								省<text>￥68000</text>
							</view>
						</view>
					</view>
				</view>
				<view class="scroll-item" @tap="ganglou(3,'刚需')">
					<view class="item-top">
						<view class="red">
							临安
						</view>
						<view class="name">
							赞成.国潮商务...
						</view>
						<image src="../../static/other/about-logo.jpg" mode=""></image>
					</view>
					<view class="item-bom">
						<view class="old">
							原价: 260万
						</view>
						<view class="new">
							<view class="newleft">
								特价
							</view>
							<view class="newright">
								省<text>￥68000</text>
							</view>
						</view>
					</view>
				</view>
				<view class="scroll-item" @tap="ganglou(3,'刚需')">
					<view class="item-top">
						<view class="red">
							临安
						</view>
						<view class="name">
							赞成.国潮商务...
						</view>
						<image src="../../static/other/about-logo.jpg" mode=""></image>
					</view>
					<view class="item-bom">
						<view class="old">
							原价: 260万
						</view>
						<view class="new">
							<view class="newleft">
								特价
							</view>
							<view class="newright">
								省<text>￥68000</text>
							</view>
						</view>
					</view>
				</view>
				<view class="scroll-item" @tap="ganglou(3,'刚需')">
					<view class="item-top">
						<view class="red">
							临安
						</view>
						<view class="name">
							赞成.国潮商务...
						</view>
						<image src="../../static/other/about-logo.jpg" mode=""></image>
					</view>
					<view class="item-bom">
						<view class="old">
							原价: 260万
						</view>
						<view class="new">
							<view class="newleft">
								特价
							</view>
							<view class="newright">
								省<text>￥68000</text>
							</view>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>
		<view class="toplist-swiper">
			<view class="tit">
				<image class="hot" src="../../static/index/index-bighot.png" mode=""></image>
				<text>家园严选</text>
				<view class="more" @tap="gobuild">
					更多楼盘
					<image src="../../static/content/right.png" mode=""></image>
				</view>
			</view>
			<scroll-view class="scroll-view" scroll-x="true">
				<view class="scroll-item">
						<view class="lefticon">
							<image src="../../static/index/index-jiang.png" mode=""></image>
						</view>
						<image class="rightimg" src="../../static/other/about-logo.jpg" mode=""></image>
						<view class="name">
							刚需楼盘榜
						</view>
						<view class="time">
							更新于2021-04-22
						</view>
						<view class="li">
							<view class="round">
							</view>
							中天溪珺庭
						</view>
						<view class="li">
							<view class="round">
							</view>
							中天溪珺庭
						</view>
						<view class="li">
							<view class="round">
							</view>
							中天溪珺庭
						</view>
						<view class="bom">
							<view class="btn">
							</view>
							<view class="msg">
								更多楼盘
								<image src="../../static/index/roundmore.png" mode=""></image>
							</view>
						</view>
				</view>
				<view class="scroll-item">
						<view class="lefticon">
							<image src="../../static/index/index-jiang.png" mode=""></image>
						</view>
						<image class="rightimg" src="../../static/other/about-logo.jpg" mode=""></image>
						<view class="name">
							刚需楼盘榜
						</view>
						<view class="time">
							更新于2021-04-22
						</view>
						<view class="li">
							<view class="round">
							</view>
							中天溪珺庭
						</view>
						<view class="li">
							<view class="round">
							</view>
							中天溪珺庭
						</view>
						<view class="li">
							<view class="round">
							</view>
							中天溪珺庭
						</view>
						<view class="bom">
							<view class="btn">
							</view>
							<view class="msg">
								更多楼盘
								<image src="../../static/index/roundmore.png" mode=""></image>
							</view>
						</view>
				</view>
			</scroll-view>
		</view>
		
		<view class="dynamic">
			<view class="dynamic-box">
				<view class="dynamic-tit">
					<text class="title">实时动态</text>
					<view class="righttop">
						新消息
						<view class="jiao">
						</view>
					</view>
					<view class="right_t" @tap="godynamic">
						<text class="more">更多动态</text>
						<image src="../../static/content/right.png" mode=""></image>
					</view>

				</view>
				<view class="dynamic-con">
					<view class="con1" @tap="godynamicdetail">
						<!-- <navigator :url="`../dynamicdetail/dynamicdetail?/id=${dynamics[0].id}`"> -->
						<view class="con1-top">
							<image :src="dynamics[0].img" mode=""></image>
							<text class="title">{{dynamics[0].name}}</text>
						</view>
						<view class="con1-bom">
							<text class="txt">{{dynamics[0].introduce}}</text>
						</view>
						<!-- </navigator> -->
					</view>
					<view class="right">
						<view class="con2">
							<navigator :url="`../dynamicdetail/dynamicdetail?id=${dynamics[1].id}`" class="nav_nav">
								<view class="con2-left">
									<text class="msg">{{dynamics[1].introduce}}</text>
								</view>
								<view class="con2-right">
									<image :src="dynamics[1].img" mode=""></image>
									<text>{{dynamics[1].name}}</text>
								</view>
							</navigator>
						</view>
						<view class="con2 con3">
							<navigator :url="`../dynamicdetail/dynamicdetail?id=${dynamics[2].id}`" class="nav_nav">
								<view class="con2-left">
									<text class="msg">{{dynamics[2].introduce}}</text>
								</view>
								<view class="con2-right">
									<image :src="dynamics[2].img" mode=""></image>
									<text>{{dynamics[2].name}}</text>
								</view>
							</navigator>
						</view>
					</view>
				</view>
			</view>
		</view>
		
		<view class="articles">
			<view class="tit">
				楼盘导购
				<view class="more" @tap="goinfos">
					更多资讯
					<image src="../../static/content/right.png" mode=""></image>
				</view>
			</view>
			<view class="article">
				<view class="left">
					<view class="name">
						不限购！半小时到未来科技城！
						临安稀缺户型即将首开！
					</view>
					<view class="msg">
						家园新房 <text>2020-05-24</text>
					</view>
				</view>
				<view class="right">
					<image src="../../static/other/about-logo.jpg" mode=""></image>
				</view>
			</view>
			<view class="article">
				<view class="left">
					<view class="name">
						不限购！半小时到未来科技城！
						临安稀缺户型即将首开！
					</view>
					<view class="msg">
						家园新房 <text>2020-05-24</text>
					</view>
				</view>
				<view class="right">
					<image src="../../static/other/about-logo.jpg" mode=""></image>
				</view>
			</view>
			<view class="article">
				<view class="left">
					<view class="name">
						不限购！半小时到未来科技城！
						临安稀缺户型即将首开！
					</view>
					<view class="msg">
						家园新房 <text>2020-05-24</text>
					</view>
				</view>
				<view class="right">
					<image src="../../static/other/about-logo.jpg" mode=""></image>
				</view>
			</view>
		</view>
		
		<view class="recommend">
			<view class="tit">
				为你推荐 
				<view class="more">
					更多楼盘
					<image src="../../static/content/right.png" mode=""></image>
				</view>
			</view>
			<prosbox :recommends="recommends"></prosbox>
		</view>
		<!-- <tabbar :type="0"></tabbar> -->
		<view class="bommorebtn" @tap="gobuild">
			更多楼盘
			<image src="../../static/index/index-green.png" mode=""></image>
		</view>
	</view>
</template>

<script>
	import tabbar from '../../components/tabbar/tabbar.vue'
	import prosbox from '../../components/pros.vue'
	export default {
		data() {
			return {
				tops: [],
				avg_prices: {},
				rigid_demand: [],
				investment: [],
				improvement: [],
				completed_houses: [],
				hot_searches: [],
				popularity: [],
				deals: [],
				dynamics: [],
				recommends: [],
				common: [],
				dynamics: [],

				style_list: {
					hot: true,
					people: false,
					jiao: false,
				},
				cityname: '杭州',
				isweixin: false,
				isover: false
			}
		},
		components: {
			tabbar,
			prosbox
		},
		onLoad(options) {
			// #ifdef  MP-WEIXIN
			this.isweixin = true
			// #endif
			console.log(options)
			this.cityname = uni.getStorageSync('cityname') || '杭州'
			let city = options.city || uni.getStorageSync('city');
			let token = uni.getStorageSync("token");
			uni.showLoading({
				title: "加载中"
			})
			uni.request({
				url: this.apiserve + '/applets/first',
				data: {
					token: token,
					city: city,
					other: uni.getStorageSync('other'),
					uuid: uni.getStorageSync('uuid')
				},
				success: (res) => {
					console.log(res);
					if (res.data.code == 200) {
						this.isover = true
						uni.hideLoading()
						this.tops = res.data.data.tops;
						this.avg_prices = res.data.data.avg_prices;
						this.rigid_demand = res.data.data.rigid_demand;
						this.investment = res.data.data.investment;
						this.improvement = res.data.data.improvement;
						this.hot_searches = res.data.data.hot_searches;
						this.completed_houses = res.data.data.completed_houses;
						this.common = this.hot_searches;
						this.popularity = res.data.data.popularity;
						this.deals = res.data.data.deals;
						this.dynamics = res.data.data.dynamics;
						this.recommends = res.data.data.recommends;
						this.cityname = res.data.data.city_info.current.short
						// #ifdef MP-BAIDU
						let header = res.data.data.common.header;
						swan.setPageInfo({
							title: header.title,
							keywords: header.keywords,
							description: header.description,
							success: res => {
								console.log('setPageInfo success', res);
							},
							fail: err => {
								console.log('setPageInfo fail', err);
							}
						})
						// #endif
					}

				}

			})
		},
		onReady() {
			var view = uni.createSelectorQuery().select(".toptitle");

			view.boundingClientRect(function(data) {

				console.log(data.height);

			}).exec();
		},
		methods: {
			gobuild() {
				uni.switchTab({
					url: `/pages/building/building`
				})
			},
			gojoin() {
				uni.navigateTo({
					url: `/pages/alliance/alliance`
				})
			},
			gomap() {
				uni.navigateTo({
					url: `/pages/map/map`
				})
			},
			goinfos() {
				uni.navigateTo({
					url: `/pages/infos/infos`
				})
			},
			gospecial() {
				uni.navigateTo({
					url: `/pageA/special/special`
				})
			},
			goweike() {
				uni.navigateTo({
					url: `/pages/weike/weike`
				})
			},
			godynamicdetail() {
				let id = this.dynamics[0].id
				console.log(id)
				uni.navigateTo({
					url: '/pages/dynamicdetail/dynamicdetail?id=' + id
				})
			},
			gotop() {
				uni.navigateTo({
					url: '/pages/top/top'
				})
			},
			godynamic() {
				uni.navigateTo({
					url: '/pages/dynamic/dynamic'
				})
			},
			ganglou(num, txt) {
				uni.navigateTo({
					url: `/pages/feature/feature?num=${num}&txt=${txt}`
				})
			},
			gohelp() {
				uni.navigateTo({
					url: '/pages/help/help'
				})
			},
			godynamic() {
				uni.navigateTo({
					url:"/pages/dynamic/dynamic"
				})
			},
			goSearch() {
				uni.navigateTo({
					url: "/pages/searchname/searchname"
				})
			},
			gopath() {
				uni.navigateTo({
					url: '/pages/path/path'
				})
			},
			hotSearch() {
				this.common = this.hot_searches;
				this.style_list.hot = true;
				this.style_list.people = false;
				this.style_list.jiao = false;

			},
			peopleClick() {
				this.common = this.popularity;
				this.style_list.hot = false;
				this.style_list.people = true;
				this.style_list.jiao = false;
			},
			jiaoClick() {
				this.common = this.deals;
				this.style_list.hot = false;
				this.style_list.people = false;
				this.style_list.jiao = true;
			},
			bangZhao() {
				uni.navigateTo({
					url: "../help/help"
				})
			}
		}
	}
</script>

<style lang="less">
	page {
		background: #F5F5F5;
		padding-bottom: 30rpx;
	}

	.toptitle {
		.status_bar {
			height: var(--status-bar-height);
			width: 50%;
		}

		color: #17181A;
		font-size: 29.88rpx;
		padding: 0 29.88rpx;
		line-height: 88rpx;
		position: fixed;
		width: 100%;
		background-color: #FFFFFF;
		top: 0;
		z-index: 9999;
	}

	.search {
		padding: 0 29.88rpx;
		margin-top: 10rpx;

		.searchbox {
			height: 71.71rpx;
			border-radius: 35.85rpx;
			background-color: #F7F7F7;
			display: flex;
			align-items: center;
			padding-left: 27.88rpx;
			margin-bottom: 10rpx;

			.search-path {
				width: 16rpx;
				height: 16rpx;
				margin-left: 10rpx;
				margin-right: 23rpx;
			}

			.cityname {
				color: #303233;
				font-size: 27.88rpx;
				margin-top: -4rpx;
			}

			.line {
				width: 1rpx;
				height: 32rpx;
				background: #D5D9DF;
			}

			.search-right {
				display: flex;
				//border-left: 0.99rpx solid #D4D4D9;
				padding-left: 35.85rpx;
				height: 72rpx;
				width: 500rpx;

				.right-icon {
					width: 31.87rpx;
					height: 31.87rpx;
					position: relative;
					top: 20rpx;
					margin-right: 11.95rpx;
				}

				.text {
					font-size: 28rpx;
					font-weight: 500;
					color: #646566;
					line-height: 72rpx;
				}
			}
		}
	}

	.searchpros {
		padding: 0 30rpx;
		line-height: 40rpx;
		position: relative;
		margin-bottom: 22rpx;
		.scrollpro {
			width:100%;
			white-space:nowrap;
			.proname {
				display: inline-block;
				font-size: 26rpx;
				color: #FFFFFF;
				margin-right: 34rpx;
			}
		}
		.prozhao {
			position: absolute;
			width: 80rpx;
			height: 40rpx;
			background: linear-gradient(0deg, #2ABF89, #2ABF89);
			right: 0;
			top: 0;
			z-index: 10;
			display: flex;
			align-items: center;
			image {
				width: 32rpx;
				height: 32rpx;
				margin-left: 10rpx;
			}
		}
	}

	.topbj {
		position: absolute;
		width: 750rpx;
		height: 320rpx;
		background: linear-gradient(180deg, #2ABF89, #2ABF89, #F5F5F5);
		top: 0;
		left: 0;
		z-index: -1;
	}

	.banner {
		width: 687.25rpx;
		margin-left: 29.88rpx;
		height: 199.2rpx;
		margin-bottom: 39.84rpx;
		border-radius: 12rpx;
	}
	
	.navs {
		margin: 0 30rpx;
		background-color: #FFFFFF;
		border-radius: 16rpx;
		margin-bottom: 20rpx;
		padding: 0 30rpx;
		.navsbox {
			display: flex;
			justify-content: space-between;
			.li {
				width: 20%;
				text-align: center;
				image {
					width: 92rpx;
					height: 92rpx;
					margin-bottom: 14rpx;
				}
				text {
					color: #181A1A;
					font-size: 24rpx;
					font-weight: bold;
					display: block;
				}
			}
		}
		.navsbox:nth-of-type(2) {
			margin-top: 36rpx;
			margin-bottom: 24rpx;
			image {
				width: 44rpx;
				height: 44rpx;
			}
			text {
				font-weight: 500;
				color: #303333;
			}
		}
		.topnew {
			border-top: 1rpx solid #EDEDED;
			.topnew-box {
				display: flex;
				align-items: center;
		
				.icon {
					width: 120rpx;
					height: 26rpx;
					margin-right: 20rpx;
				}
		
				.swiper {
					height: 87rpx;
					flex: 1;
		
					.swiper-item {
						line-height: 87rpx;
						color: #646566;
						font-size: 27.88rpx;
						width: 478rpx;
						overflow: hidden;
						text-overflow: ellipsis;
						white-space: nowrap;
					}
				}
			}
		}
	}

	.help {
		position: relative;
		padding: 0 30rpx;
		margin-bottom: 20rpx;

		image {
			width: 100%;
			height: 128rpx;
		}

		.help-btn {
			display: block;
			width: 148rpx;
			height: 52rpx;
			position: absolute;
			background: linear-gradient(270deg, #28C567, #61E68C);
			text-align: center;
			line-height: 52rpx;
			color: #FFFFFF;
			font-size: 26rpx;
			top: 38rpx;
			right: 60rpx;
			border-radius: 27.88rpx;
		}
	}

	.feature {
		background-color: #fff;
		margin: 0 30rpx;
		margin-bottom: 20rpx;
		border-radius: 16rpx;
		height: 408rpx;
		.feature-tit {
			color: #17181A;
			font-size: 34rpx;
			font-weight: bold;
			margin-left: 30rpx;
			padding-top: 38rpx;
			.more {
				float: right;
				color: #7D7E80;
				font-size: 24rpx;
				margin-right: 30rpx;
				image {
					width: 24rpx;
					height: 24rpx;
					margin-left: 8rpx;
					margin-bottom: -2rpx;
				}
			}
		}

		.scroll-view {
			margin-top: 31.87rpx;
			width: 100%;
			white-space: nowrap;

			.scroll-item {
				margin-right: 19.92rpx;
				display: inline-block;
				width: 260rpx;
				height: 264rpx;
				overflow-wrap: break-word;
				white-space: normal;
				border-radius: 12rpx;
				box-shadow: 0px 0px 30px 0px rgba(0, 0, 0, 0.04);
				.item-top {
					position: relative;
					height: 144rpx;
					border-radius: 12rpx 12rpx 0 0;
					overflow: hidden;
					
					image {
						width: 100%;
						height: 100%;
					}
					.red {
						width: 64rpx;
						height: 30rpx;
						border-radius: 12rpx 0 12rpx 0;
						position: absolute;
						top: 0;
						left: 0;
						text-align: center;
						line-height: 30rpx;
						background-color: #FF5959;
						font-size: 20rpx;
						color: #FFFFFF;
					}
					.name {
						width: 200rpx;
						color: #FFFFFF;
						font-size: 28rpx;
						font-weight: bold;
						position: absolute;
						bottom: 20rpx;
						left: 50%;
						margin-left: -100rpx;
						overflow: hidden;
						text-overflow:ellipsis;
						white-space: nowrap;
					}
				}
				.item-bom {
					padding-left: 20rpx;
					.old {
						color: #969799;
						font-size: 24rpx;
						padding-top: 22rpx;
						text-decoration: line-through;
						margin-bottom: 16rpx;
					}
					.new {
						display: flex;
						width: 220rpx;
						height: 38rpx;
						border-radius: 12rpx 6rpx 12rpx 6rpx;
						overflow: hidden;
						background-color: #FFEBEB;
						.newleft {
							width: 78rpx;
							height: 38rpx;
							line-height: 38rpx;
							color: #FF4040;
							font-size: 22rpx;
							display: flex;
							justify-content: center;
							align-items: center;
							image {
								width: 24rpx;
								height: 24rpx;
								margin-right: 4rpx;
							}
						}
						.newright {
							width: 126rpx;
							height: 38rpx;
							color: #FFFFFF;
							font-size: 22rpx;
							line-height: 38rpx;
							padding-left: 20rpx;
							background: url(../../static/index/index-rightimg.png);
							text {
								font-size: 24rpx;
							}
						}
					}
				}
			}

			.scroll-item:nth-of-type(1) {
				margin-left: 30rpx;
			}
		}
	}

	.toplist-swiper {
		margin: 0 30rpx;
		margin-bottom: 20rpx;
		background-color: #FFFFFF;
		border-radius: 16rpx;
		height: 552rpx;
		.tit {
			padding: 40rpx 30rpx;
			.hot {
				width: 36rpx;
				height: 36rpx;
				margin-right: 8rpx;
				margin-bottom: -2rpx;
			}
			text {
				color: #17181A;
				font-size: 34rpx;
			}
			.more {
				float: right;
				color: #7D7E80;
				font-size: 24rpx;
				image {
					width: 24rpx;
					height: 24rpx;
					margin-left: 8rpx;
					margin-bottom: -2rpx;
				}
			}
		}
		.scroll-view {
			width: auto;
			white-space: nowrap;
			padding-bottom: 10rpx;

			.scroll-item {
				width: 440rpx;
				height: 400rpx;
				margin-right: 30rpx;
				display: inline-block;
				overflow-wrap: break-word;
				white-space: normal;
				border-radius: 12rpx;
				position: relative;
				background-color: #F7F7F7;
				.lefticon {
					width: 52rpx;
					height: 52rpx;
					background-color: #FFF0D1;
					position: absolute;
					left: 0;
					top: 0;
					border-radius: 12rpx 0 12rpx 0;
					display: flex;
					justify-content: center;
					align-items: center;
					image {
						width: 36rpx;
						height: 36rpx;
					}
				}
				.rightimg {
					position: absolute;
					width: 180rpx;
					height: 132rpx;
					border-radius: 6rpx;
					right: -10rpx;
					top: -12rpx;
				}
				.name {
					color: #AF772D;
					font-size: 28rpx;
					font-weight: bold;
					padding-top: 18rpx;
					margin-left: 72rpx;
					margin-bottom: 22rpx;
				}
				.time {
					color: #646466;
					font-size: 20rpx;
					margin-left: 30rpx;
					margin-bottom: 40rpx;
				}
				.li {
					display: flex;
					color: #4B4D4C;
					font-size: 28rpx;
					margin-bottom: 20rpx;
					align-items: center;
					margin-left: 30rpx;
					.round {
						background-color: #AF772D;
						width: 6rpx;
						height: 6rpx;
						border-radius: 50%;
						margin-right: 12rpx;
					}
				}
				.bom {
					position: absolute;
					bottom: 0;
					left: 0;
					width: 440rpx;
					height: 80rpx;
					background-color: #F7F7F7;
					.btn {
						width: 300rpx;
						height: 80rpx;
						box-shadow: 0px 8rpx 15rpx 0px rgba(4, 0, 0, 0.05);
						position: absolute;
						left: 70rpx;
						bottom: 0;
					}
					.msg {
						position: absolute;
						z-index: 10;
						bottom: 0;
						left: 0;
						width: 440rpx;
						height: 80rpx;
						background-color: #F7F7F7;
						text-align: center;
						line-height: 80rpx;
						color: #737980;
						font-size: 24rpx;
						image {
							width: 24rpx;
							height: 24rpx;
							margin-left: 10rpx;
							margin-bottom: -2rpx;
						}
					}
				}
			}
			.scroll-item:nth-of-type(1){
				margin-left: 30rpx;
			}
		}
	}

	.dynamic {
		margin: 0 30rpx;
		background-color: #FFFFFF;
		border-radius: 16rpx;
		padding: 0 30rpx;
		height: 434rpx;
		margin-bottom: 20rpx;
		.dynamic-box {
			.dynamic-tit {
				display: flex;
				margin-bottom: 35.85rpx;
				justify-content: space-between;
				position: relative;
				padding-top: 30rpx;
				.title {
					color: #17181A;
					font-size: 33.86rpx;
					font-weight: bold;
				}
				.righttop {
					position: absolute;
					width: 64rpx;
					height: 26rpx;
					border-radius: 6rpx 6rpx 6rpx 0;
					background: #FF4040;
					text-align: center;
					line-height: 26rpx;
					color: #FFFFFF;
					font-size: 18rpx;
					font-weight: bold;
					left: 140rpx;
					
					.jiao {
						position: absolute;
						border: 10rpx solid rgba(0,0,0,0);
						border-left-color: #FF4040;
						left: 0;
						bottom: -8rpx;
					}
				}
				.right_t {
					.more {
						color: #969799;
						font-size: 25.89rpx;
						margin-left: auto;
					}

					image {
						width: 24rpx;
						height: 24rpx;
					}
				}

			}

			.dynamic-con {
				display: flex;

				.con1 {
					width: 278.88rpx;
					height: 282.86rpx;
					border-radius: 11.95rpx;
					overflow: hidden;
					margin-right: 19.92rpx;

					.con1-top {
						position: relative;
						height: 151.39rpx;

						image {
							width: 100%;
							height: 151.39rpx;
						}

						.title {
							position: absolute;
							color: #FFFFFF;
							left: 19.92rpx;
							bottom: 15.93rpx;
							font-weight: bold;
							font-size: 25.89rpx;
						}
					}

					.con1-bom {
						height: 131.47rpx;
						background: #F5F6F7;
						display: flex;
						justify-content: center;
						align-items: center;

						.txt {
							width: 229.08rpx;
							color: #646466;
							font-size: 23.9rpx;
							line-height: 33.86rpx;
							display: block;
							display: -webkit-box;
							-webkit-box-orient: vertical;
							-webkit-line-clamp: 2;
							overflow: hidden;
						}
					}
				}

				.right {
					width: 388.44rpx;

					.con2 {
						width: 100%;
						height: 131.47rpx;
						border-radius: 11.95rpx;
						overflow: hidden;
						display: flex;
						margin-bottom: 19.92rpx;

						.nav_nav {
							width: 100%;
							height: 131.47rpx;
							border-radius: 11.95rpx;
							overflow: hidden;
							display: flex;
							margin-bottom: 19.92rpx;

							.con2-left {
								width: 249rpx;
								height: 131.47rpx;
								display: flex;
								justify-content: center;
								align-items: center;
								background: #F5F6F7;

								.msg {
									display: block;
									color: #646466;
									font-size: 23.9rpx;
									width: 215.13rpx;
									line-height: 33.86rpx;
									display: block;
									display: -webkit-box;
									-webkit-box-orient: vertical;
									-webkit-line-clamp: 2;
									overflow: hidden;
								}
							}

							.con2-right {
								width: 139.44rpx;
								height: 131.47rpx;
								position: relative;

								image {
									width: 139.44rpx;
									height: 131.47rpx;
								}

								text {
									display: block;
									position: absolute;
									text-align: center;
									width: 103.58rpx;
									color: #FFFFFF;
									font-size: 25.89rpx;
									top: 31.87rpx;
									left: 11.95rpx;
									display: -webkit-box;
									-webkit-box-orient: vertical;
									-webkit-line-clamp: 2;
									overflow: hidden;
								}
							}
						}
					}

					.con3 {
						margin-bottom: 0;
					}
				}
			}
		}
	}

	.articles {
		margin: 0 30rpx;
		background-color: #fff;
		border-radius: 16rpx;
		margin-bottom: 20rpx;
		padding: 0 30rpx;
		padding-bottom: 20rpx;
		.tit {
			color: #17181A;
			font-size: 34rpx;
			margin-bottom: 36rpx;
			font-weight: bold;
			padding-top: 38rpx;
			.more {
				float: right;
				color: #7D7E80;
				font-size: 24rpx;
				font-weight: 500;
				image {
					width: 24rpx;
					height: 24rpx;
					margin-left: 8rpx;
					margin-bottom: -2rpx;
				}
			}
		}
		.article {
			display: flex;
			margin-bottom: 20rpx;
			.left {
				position: relative;
				flex: 1;
				.name {
					color: #323233;
					font-size: 30rpx;
					line-height: 40rpx;
				}
				.msg {
					color: #969799;
					font-size: 22rpx;
					position: absolute;
					bottom: 18rpx;
				}
			}
			.right {
				margin-left: 30rpx;
				width: 200rpx;
				height: 140rpx;
				border-radius: 12rpx;
				overflow: hidden;
				image {
					width: 100%;
					height: 100%;
				}
			}
		}
	}

	.recommend {
		margin: 0 30rpx;
		background-color: #fff;
		border-radius: 16rpx;
		padding: 0 30rpx;
		margin-bottom: 30rpx;
		padding-bottom: 30rpx;

		.tit {
			color: #17181A;
			font-size: 33.86rpx;
			margin-bottom: 31.87rpx;
			font-weight: bold;
			padding-top: 38rpx;
			.more {
				float: right;
				color: #7D7E80;
				font-size: 24rpx;
				font-weight: 500;
				image {
					width: 24rpx;
					height: 24rpx;
					margin-left: 8rpx;
					margin-bottom: -2rpx;
				}
			}
		}
	}
	
	.bommorebtn {
		margin: 0 30rpx;
		height: 92rpx;
		border-radius: 16rpx;
		display: flex;
		justify-content: center;
		align-items: center;
		color: #38916C;
		font-size: 30rpx;
		font-weight: bold;
		background-color: #D5EDDF;
		image {
			width: 32rpx;
			height: 32rpx;
		}
	}
</style>
