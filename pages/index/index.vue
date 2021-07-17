<template>
	<view class="content" v-if="isover">
		<!-- <view class="toptitle">
			<view class="status_bar">
			</view>
			<text>家园新房</text>
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
				<view class="topshi" v-if="tebtn">
					<view class="topbb">
					</view>
					<view class="left">
						<view class="t1">
							点此添加 我的小程序
						</view>
						<view class="t1">
							找房更方便
						</view>
					</view>
					<view class="right" @tap="tebtn = false">
						<image src="../../static/close.png" mode=""></image>
					</view>
				</view>
			</view>
		</view>
		<view class="searchpros">
			<view class="prozhao" @tap="gobuild">
				<!-- <image src="../../static/index/index-yy.png" mode=""></image> -->
				<image src="../../static/index/index-down.png" mode=""></image>
			</view>
			<scroll-view class="scrollpro" scroll-x="true">
				<view class="proname" v-for="item in recommends_head" :key="item.id" @tap="gopro(item.id)">
					{{item.name}}
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
				<view class="li" @tap="gonearrail">
					<image src="../../static/index/index-ditie.png" mode=""></image>
					<text>地铁房</text>
				</view>
				<view class="li" v-if="!isweixin" @tap="gospecial">
					<image src="../../static/index/index-tejia.png" mode=""></image>
					<text>特价房</text>
				</view>
				<view class="li" @tap="gojoin">
					<image src="../../static/index/index-join.png" mode=""></image>
					<text>刚需房</text>
				</view>
				<view class="li" @tap="gomap">
					<image src="../../static/index/index-map.png" mode=""></image>
					<text>地图找房</text>
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
				<view class="li" @tap="gowenda">
					<image src="../../static/index/index-question.png" mode=""></image>
					<text>楼盘问答</text>
				</view>
			</view>
			<view class="topnew">
				<view class="topnew-box">
					<image class="icon" src="../../static/index/index-topnew.png" mode=""></image>
					<swiper class="swiper" :vertical="true" :circular="true" :autoplay="true" interval="2000">
						<swiper-item class="swiper-item" v-for="item in news" :key="item.id">
							<navigator :url="`../info/info?id=${item.id}`">
								<view class="swiper-item uni-bg-red">{{item.name}}</view>
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
		<view class="feature" v-if="special_price.length">
			<view class="feature-tit">今日特价房<view class="more" @tap="gospecial">更多楼盘<image
						src="../../static/content/right.png" mode=""></image>
				</view>
			</view>
			<scroll-view class="scroll-view" scroll-x="true">
				<view class="scroll-item" @tap="gopro(item.id)" v-for="item in special_price" :key="item.id">
					<view class="item-top">
						<view class="red">
							{{item.country.substr(0,2)}}
						</view>
						<view class="name">
							{{item.name}}
						</view>
						<image :src="item.img" mode=""></image>
					</view>
					<view class="item-bom">
						<view class="old">
							原价: {{(item.original_total/10000).toFixed(0)}}万
						</view>
						<view class="new">
							<view class="newleft">
								<image src="../../static/index/index-hot.png" mode=""></image>
								特价
							</view>
							<view class="newright">
								省<text>￥{{item.diff}}</text>
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
					<view class="scroll-item-box">
						<view class="lefticon">
							<image src="../../static/index/index-jiang.png" mode=""></image>
						</view>
						<image class="rightimg" :src="rigid_demands[0].img" mode=""></image>
						<view class="name">
							刚需楼盘榜
						</view>
						<view class="time">
							更新于{{starttime}}
						</view>
						<view class="libox">
						<view class="li" v-for="(item,key) in rigid_demands" :key="item.id" @tap="gopro(item.id)" v-if="key<3">
							<view class="round">
							</view>
							{{item.name}}
						</view>
						</view>
						<view class="bom" @tap="setbuild(3)">
							<view class="btn">
							</view>
							<view class="msg">
								更多楼盘
								<image src="../../static/index/roundmore.png" mode=""></image>
							</view>
						</view>
					</view>
				</view>
				<view class="scroll-item">
					<view class="scroll-item-box">
						<view class="lefticon">
							<image src="../../static/index/index-jiang.png" mode=""></image>
						</view>
						<image class="rightimg" :src="brands[0].img" mode=""></image>
						<view class="name">
							品牌楼盘榜
						</view>
						<view class="time">
							更新于{{starttime}}
						</view>
						<view class="libox">
							<view class="li" v-for="(item,key) in brands" :key="item.id" @tap="gopro(item.id)" v-if="key<3">
								<view class="round">
								</view>
								{{item.name}}
							</view>
						</view>
						
						<view class="bom">
							<view class="btn">
							</view>
							<view class="msg" @tap="setbuild(4)">
								更多楼盘
								<image src="../../static/index/roundmore.png" mode=""></image>
							</view>
						</view>
					</view>
					
				</view>
				<view class="scroll-item">
					<view class="scroll-item-box">
						<view class="lefticon">
							<image src="../../static/index/index-jiang.png" mode=""></image>
						</view>
						<image class="rightimg" :src="railways[0].img" mode=""></image>
						<view class="name">
							地铁楼盘榜
						</view>
						<view class="time">
							更新于{{starttime}}
						</view>
						<view class="libox">
						<view class="li" v-for="(item,key) in railways" :key="item.id" @tap="gopro(item.id)" v-if="key<3">
							<view class="round">
							</view>
							{{item.name}}
						</view>
						</view>
						<view class="bom" @tap="setbuild(6)">
							<view class="btn">
							</view>
							<view class="msg">
								更多楼盘
								<image src="../../static/index/roundmore.png" mode=""></image>
							</view>
						</view>
					</view>
					
				</view>
				<view class="scroll-item">
					<view class="scroll-item-box">
						<view class="lefticon">
							<image src="../../static/index/index-jiang.png" mode=""></image>
						</view>
						<image class="rightimg" :src="educations[0].img" mode=""></image>
						<view class="name">
							现房楼盘榜
						</view>
						<view class="time">
							更新于{{starttime}}
						</view>
						<view class="libox">
						<view class="li" v-for="(item,key) in educations" :key="item.id" @tap="gopro(item.id)" v-if="key<3">
							<view class="round">
							</view>
							{{item.name}}
						</view>
						</view>
						<view class="bom" @tap="setbuild(8)">
							<view class="btn">
							</view>
							<view class="msg">
								更多楼盘
								<image src="../../static/index/roundmore.png" mode=""></image>
							</view>
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
							<text class="txt">{{dynamics[0].content}}</text>
						</view>
						<!-- </navigator> -->
					</view>
					<view class="right">
						<view class="con2">
							<navigator :url="`../dynamicdetail/dynamicdetail?id=${dynamics[1].id}`" class="nav_nav">
								<view class="con2-left">
									<text class="msg">{{dynamics[1].content}}</text>
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
									<text class="msg">{{dynamics[2].content}}</text>
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
			<view class="article" v-for="item in project_articles" :key="item.id" @tap="goinfo(item.id)">
				<view class="left">
					<view class="name">
						{{item.title}}
					</view>
					<view class="msg">
						{{item.source}} <text>{{item.time}}</text>
					</view>
				</view>
				<view class="right">
					<image :src="item.img" mode=""></image>
				</view>
			</view>
		</view>

		<view class="recommend">
			<view class="tit">
				为你推荐
				<view class="more" @tap="gobuild">
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
		<view class="fixbom" v-if="logintype">
			<image @tap="logintype=false" src="../../static/index/indexclose.png" mode=""></image>
			<view class="txt">
				立即登录咨询找房更方便
			</view>
			<button open-type="getPhoneNumber" hover-class="none" @getphonenumber="getPhoneNumber" class="fixbtn">
				立即登录
			</button>
		</view>
	</view>
</template>

<script>
	import tabbar from '../../components/tabbar/tabbar.vue'
	import prosbox from '../../components/pros.vue'
	export default {
		data() {
			return {
				special_price: [],
				project_articles: [],
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
				isover: false,
				tebtn: false,
				logintype: false,
				railways: [],
				brands: [],
				rigid_demands: [],
				educations: [],
				recommends_head: [],
				news: [],
				starttime: ''
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
			let that = this
			let uuid = uni.getStorageSync('uuid')
			let city = uni.getStorageSync('city')
			let ip = uni.getStorageSync('ip')
			let arr = getCurrentPages()
			let url = arr[arr.length - 1].route
			let host = this.host
			let pp = {
				controller: "Info",
				action: "register",
				params: {
					city: city,
					project: '',
					ip: ip,
					url: url,
					uuid: uuid,
					host: host
				},
			};
			this.$store.state.socket.send({
				data: JSON.stringify(pp)
			});
			if (!uni.getStorageSync('token')) {
				// this.logintype = true
				this.tebtn = true
				setTimeout(() => {
					// that.logintype = false
					that.tebtn = false
				}, 12000)
			}
			console.log(options)
			this.cityname = uni.getStorageSync('cityname') || '杭州'
			let token = uni.getStorageSync("token");
			uni.showLoading({
				title: "加载中"
			})
			let date = new Date()
			console.log(date.getFullYear(), 'year')
			this.starttime = date.getFullYear() + '-' + (date.getMonth() + 1 >= 10 ? (date.getMonth() + 1) : '0' + (date
				.getMonth() + 1)) + '-' + (date.getDate() >= 10 ? date.getDate() : '0' + date.getDate())
			uni.request({
				url: this.javaserve + '/applets/jy',
				data: {
					city: city,
					other: uni.getStorageSync('other'),
					uuid: uni.getStorageSync('uuid')
				},
				success: (res) => {
					console.log(res, 'res');
					if (res.data.status == 200) {
						this.isover = true
						this.dynamics = res.data.data.dynamics;
						this.recommends = res.data.data.footRecommend;
						this.project_articles = res.data.data.project_articles
						this.special_price = res.data.data.special_price
						this.railways = res.data.data.railways
						this.brands = res.data.data.brands
						this.rigid_demands = res.data.data.rigid_demands
						this.educations = res.data.data.educations
						this.recommends_head = res.data.data.recommends_head
						this.news = res.data.data.news
						this.cityname = res.data.data.city.name
						uni.hideLoading()
						// #ifdef MP-BAIDU
						let header = res.data.data.header;
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
						console.log(this.dynamics, 'dynamics')
					}
				},
			})
		},
		onReady() {
			var view = uni.createSelectorQuery().select(".toptitle");
			
			view.boundingClientRect(function(data) {

				console.log(data.height);

			}).exec();
		},
		methods: {
			setbuild(id) {
				uni.setStorageSync('indexfeature', id)
				uni.switchTab({
					url: '/pages/building/building'
				})
			},
			getPhoneNumber(e) {
				let that = this
				console.log(e)
				// #ifdef  MP-BAIDU
				if (e.detail.errMsg == 'getPhoneNumber:fail auth deny') {} else {
					uni.setStorageSync('pass', true)
					that.pass = true
					console.log(that.pass)
					let session = uni.getStorageSync('session')
					if (session) {
						uni.request({
							url: "https://java.edefang.net/applets/jy/decrypt",
							method: "post",
							data: {
								iv: e.detail.iv,
								ciphertext: e.detail.encryptedData,
								sessionKey: session,
								other: uni.getStorageSync("other"),
								uuid: uni.getStorageSync("uuid"),
							},
							header: {
								"Content-Type": "application/x-www-form-urlencoded;charset=utf-8",
							},
							success: (res) => {
								console.log(res);
								let tel = res.data.data.mobile;
								uni.setStorageSync('phone', tel)
								let openid = uni.getStorageSync('openid')
								that.tel = tel.substr(0, 3) + '****' + tel.substr(7)
								uni.request({
									url: "https://java.edefang.net/applets/jy/login",
									method: "POST",
									header: {
										"Content-Type": "application/x-www-form-urlencoded;charset=utf-8",
									},
									data: {
										phone: tel,
										openid: openid,
										uuid: uni.getStorageSync("uuid"),
										city: uni.getStorageSync("city"),
									},
									success: (res) => {
										console.log(res);
										uni.setStorageSync("token", res.data.data);
										that.logintype = false
										uni.showToast({
											title: '登录成功',
											icon: "none"
										})
									}
								})

							}
						})
					} else {
						swan.getLoginCode({
							success: (res) => {
								console.log(res.code);
								uni.request({
									url: "https://java.edefang.net/applets/jy/session_key/get",
									method: "get",
									data: {
										code: res.code,
									},
									success: (res) => {
										console.log(res);
										uni.setStorageSync("openid", res.data.data.openid);
										uni.setStorageSync("session", res.data.data.session_key);
										uni.request({
											url: "https://java.edefang.net/applets/jy/decrypt",
											data: {
												ciphertext: e.detail.encryptedData,
												iv: e.detail.iv,
												sessionKey: res.data.data.session_key,
											},
											method: "POST",
											header: {
												"Content-Type": "application/x-www-form-urlencoded;charset=utf-8",
											},
											success: (res) => {
												console.log(res);
												let tel = res.data.data.mobile;
												uni.setStorageSync('phone', tel)
												let openid = uni.getStorageSync(
													'openid')
												that.tel = tel.substr(0, 3) + '****' +
													tel.substr(7)
												uni.request({
													url: "https://java.edefang.net/applets/jy/login",
													method: "POST",
													header: {
														"Content-Type": "application/x-www-form-urlencoded;charset=utf-8",
													},
													data: {
														phone: tel,
														openid: openid,
														uuid: uni
															.getStorageSync(
																"uuid"),
														city: uni
															.getStorageSync(
																"city"),
													},
													success: (res) => {
														console.log(res);
														uni.setStorageSync(
															"token",
															res.data
															.data);
														that.logintype =
															false
														uni.showToast({
															title: '登录成功',
															icon: "none"
														})
													}
												})
											}
										})

									}
								})
							}
						});

					}
				}
				// #endif
				// #ifdef  MP-WEIXIN
				console.log(e)
				if (e.detail.errMsg == "getPhoneNumber:ok") {
					uni.login({
						provider: 'weixin',
						success: function(res) {
							console.log(res)
							uni.request({
								url: 'https://ll.edefang.net/api/weichat/jscode2session',
								method: 'get',
								data: {
									code: res.code,
									other: uni.getStorageSync('other'),
									uuid: uni.getStorageSync('uuid')
								},
								success: (res) => {
									console.log(res)
									uni.setStorageSync('openid', res.data.data.openid)
									uni.setStorageSync('session', res.data.data.session_key)
									uni.request({
										url: "https://ll.edefang.net/api/weichat/decryptData",
										data: {
											data: e.detail.encryptedData,
											iv: e.detail.iv,
											sessionKey: res.data.data.session_key,
											other: uni.getStorageSync('other'),
											uuid: uni.getStorageSync('uuid')
										},
										success: (res) => {
											let data = JSON.parse(res.data.message)
											let tel = data.purePhoneNumber
											uni.setStorageSync('phone', tel)
											let openid = uni.getStorageSync('openid')
											that.tel = tel.substr(0, 3) + '****' + tel
												.substr(7)
											uni.request({
												url: "https://api.edefang.net/applets/login",
												method: 'GET',
												data: {
													phone: tel,
													openid: openid,
													other: uni
														.getStorageSync(
															'other'),
													uuid: uni
														.getStorageSync(
															'uuid')
												},
												success: (res) => {
													uni.setStorageSync(
														'token',
														res.data
														.token)
													that.logintype = false
													uni.showToast({
														title: '登录成功',
														icon: "none"
													})
												}
											})
										}
									})
								}
							})
						}
					})
				}
				// #endif
			},
			gobuild() {
				uni.switchTab({
					url: `/pages/building/building`
				})
			},
			gonearrail() {
				uni.setStorageSync('naerrail', true)
				uni.switchTab({
					url: `/pages/building/building`
				})
			},
			gojoin() {
				uni.setStorageSync('isgangxu', true)
				uni.switchTab({
					url: `/pages/building/building`
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
			goinfo(id) {
				uni.navigateTo({
					url: "/pages/info/info?id=" + id
				})
			},
			gopro(id) {
				uni.navigateTo({
					url: "/pageA/content/content?id=" + id
				})
			},
			gospecial() {
				uni.setStorageSync('isspecial', 1)
				uni.switchTab({
					url: `/pages/building/building`
				})
			},
			gowenda() {
				uni.navigateTo({
					url: `/pages/allwenda/allwenda`
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
			// 废弃
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
					url: "/pages/dynamic/dynamic"
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
		position: relative;

		.topshi {
			z-index: 500;
			position: fixed;
			width: 360rpx;
			height: 100rpx;
			border-radius: 12rpx;
			background: rgba(0, 0, 0, 0.9);
			display: flex;
			right: 54rpx;
			top: 10rpx;
			align-items: center;

			.left {
				padding: 0 26rpx 0 24rpx;
			}

			.topbb {
				position: absolute;
				border: 10rpx solid transparent;
				border-bottom-color: rgba(0, 0, 0, 0.9);
				top: -20rpx;
				right: 90rpx;
			}

			.t1 {
				color: #E6E6E6;
				font-size: 28rpx;
			}

			.right {
				image {
					width: 24rpx;
					height: 24rpx
				}
			}
		}

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
				width: 464rpx;

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
			width: 100%;
			white-space: nowrap;

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
		padding: 0 30rpx;
		padding-top: 38rpx;
		position: relative;
		top: -20rpx;
		.navsbox {
			display: flex;
			justify-content: space-between;
			
			.li {
				width: 20%;
				text-align: center;

				image {
					width: 92rpx;
					height: 92rpx;
					margin-bottom: 10rpx;
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
			margin-bottom: 22rpx;

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
			border-radius: 16rpx;
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
						text-overflow: ellipsis;
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
		padding-bottom: 33rpx;
		.tit {
			padding: 40rpx 30rpx;
			padding-top: 37rpx;
			padding-bottom: 30rpx;
			.hot {
				width: 36rpx;
				height: 36rpx;
				margin-right: 8rpx;
				margin-bottom: -2rpx;
			}

			text {
				color: #17181A;
				font-size: 34rpx;
				font-weight: bold;
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
			height: 415rpx;
			.scroll-item {
				
				margin-right: 30rpx;
				display: inline-block;
				overflow-wrap: break-word;
				white-space: nowrap;
				padding-top: 12rpx;
				
				.scroll-item-box{
					background-color: #F7F7F7;
					position: relative;
					width: 440rpx;
					height: 400rpx;
					border-radius: 12rpx;
				}
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
					margin-bottom: 20rpx;
				}

				.time {
					color: #646466;
					font-size: 20rpx;
					margin-left: 30rpx;
					margin-bottom: 42rpx;
				}
				.libox {
					height: 180rpx;
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
					position: relative;
					bottom: 0;
					left: 0;
					width: 440rpx;
					height: 78rpx;
					background-color: #F7F7F7;
					border-radius: 0 0 12rpx 12rpx;

					.btn {
						width: 300rpx;
						height: 78rpx;
						box-shadow: 0px -8rpx 15rpx 0px rgba(4, 0, 0, 0.05);
						position: absolute;
						left: 70rpx;
						bottom: 0;
					}

					.msg {
						position: relative;
						z-index: 10;
						bottom: 0;
						left: 0;
						width: 440rpx;
						height: 78rpx;
						background-color: #F7F7F7;
						text-align: center;
						line-height: 78rpx;
						color: #737980;
						font-size: 24rpx;
						border-radius: 0 0 12rpx 12rpx;

						image {
							width: 24rpx;
							height: 24rpx;
							margin-left: 10rpx;
							margin-bottom: -2rpx;
						}
					}
				}
			}

			.scroll-item:nth-of-type(1) {
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
				padding-top: 31.5rpx;

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
						border: 10rpx solid rgba(0, 0, 0, 0);
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
					width: 265rpx;
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
					width: 345rpx;

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
									width: 180rpx;
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
			margin-bottom: 32rpx;
			font-weight: bold;
			padding-top: 35.5rpx;

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
					display: -webkit-box;
					-webkit-box-orient: vertical;
					-webkit-line-clamp: 2;
					overflow: hidden;
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
			margin-bottom: 31rpx;
			font-weight: bold;
			padding-top: 36rpx;

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

	.fixbom {
		width: 100%;
		height: 100rpx;
		position: fixed;
		bottom: 0;
		left: 0;
		z-index: 500;
		background: rgba(0, 0, 0, 0.8);
		display: flex;
		align-items: center;

		image {
			width: 48rpx;
			height: 48rpx;
			position: absolute;
			left: 0;
			top: 0;
		}

		.txt {
			color: #E6E6E6;
			font-size: 30rpx;
			margin-left: 74rpx;
			margin-right: 162rpx;
		}

		.fixbtn {
			width: 156rpx;
			height: 56rpx;
			border-radius: 6rpx;
			background: linear-gradient(270deg, #28C567, #81DB85);
			text-align: center;
			line-height: 56rpx;
			color: #FFFFFF;
			font-size: 28rpx;
			padding: 0;
		}
	}
</style>
