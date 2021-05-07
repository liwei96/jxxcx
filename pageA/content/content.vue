<template>
	<view class="detail">
		<!-- 顶部悬浮 -->
		<view class="fixed_top" v-if="fixed_show">
			<view :class="{active:class_fixed.huxing}" @tap="to('huxing_tit',1)">户型</view>
			<view :class="{active:class_fixed.dongtai}" @tap="to('dongtai',2)">动态</view>
			<view :class="{active:class_fixed.fenxi}" @tap="to('fenxi',3)">分析</view>
			<view :class="{active:class_fixed.zhou_pei}" @tap="to('zhou_pei',4)">配套</view>
		</view>
		<view class="lunbo">
			<swiper class="swiper" :autoplay="true">
				<swiper-item v-for="(item,key) in pro_img" :key="key">
					<view class="swiper-item uni-bg-red">
						<navigator :url="`/pages/piclist/piclist?id=${detail.id}`">
							<image :src="item.small" mode=""></image>
						</navigator>
					</view>
				</swiper-item>
			</swiper>
			<view class="topimgbtn">
				<text :class="{active:style_list.e_active}" @tap="showEffect">效果图</text>
				<text :class="{active:style_list.hu_active}" @tap="showHuxing">户型图</text>
				<text :class="{active:style_list.hu_active}" @tap="showHuxing">交通图</text>
			</view>
			<text class="total">共{{total}}张</text>
			<button open-type="getPhoneNumber" hover-class="none"
				@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+收藏',101,'收藏',2)" class="shou"
				v-if="!pass&&!weixin">
				<image :src="shou_old" v-if="collect==0"></image>
				<image :src="has_shou" v-if="collect==1"></image>
			</button>
			<view v-if="pass||weixin" class="shou" @tap="goShou">
				<image :src="shou_old" v-if="collect==0"></image>
				<image :src="has_shou" v-if="collect==1"></image>
			</view>
		</view>
		<!-- 项目信息 -->
		<view class="name_box">
			<view class="top">
				<view class="top_name">
					<view class="name">{{detail.name}}</view>
					<text class="tese">
						<text class="one" v-if="detail.state">{{detail.state}}</text>
						<text class="other" v-if="detail.type">{{detail.type}}</text>
						<text class="other" v-if="detail.railway">{{detail.railway}}</text>
						<text class="other" v-for="(item,index) in detail.features" :key="index">{{item}}</text>
					</text>
				</view>
				<view class="right_gong">
					<view class="duibi" @tap="goDuibi(detail.id)">
						<image src="../../static/content/duibi.png"></image>
						<text>加入对比</text>
					</view>
				</view>
			</view>
			<view class="huiimg">
				<view class="btn">
					一键咨询
					<image src="../../static/index/roundmore.png" mode=""></image>
				</view>
			</view>
			<view class="detail_list">
				<view class="list_top">
					<view class="list_left">
						<view class="price">
							<text class="left">均价:</text> <text class="strong_price">{{detail.single_price}}</text><text
								class="dan">元/m²</text>
						</view>
						<view class="kai_time">
							<text class="left">开盘:</text> <text class="com">{{detail.opentime}}</text>
						</view>
						<view class="address" @tap="goweb">
							<text class="left">地址:</text> <text class="com">{{detail.address}}</text>
						</view>
						<view class="zhu">
							<text>注：</text> <text>以上价格为开发商报价，可联系咨询师咨询最低价</text>
						</view>
					</view>
					<view class="ref_nav" @tap="goDetail">
						<text>详细信息</text>
						<image src="../../static/content/right.png" mode="" class="nav"></image>
					</view>
					<image class="right_nav" src="../../static/content/address.png" mode="" @tap="goweb"></image>
				</view>
				<view class="baoming_btn">
					<view class="btn_box">
						<button open-type="getPhoneNumber"
							@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+查询最底价',105,'咨询楼盘底价')"
							hover-class="none" v-if="!pass&&!weixin">
							<image src="../../static/content/dijia.png"></image>查询最底价
						</button>
						<view class="btn01" @tap="baoMing(detail.id,'项目落地页+查询最底价',105,'咨询楼盘底价', 1)" v-if="pass||weixin">
							<image src="../../static/content/dijia.png"></image>查询最底价
						</view>
						<button open-type="getPhoneNumber"
							@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+变价通知我',91,'变价通知我')"
							hover-class="none" v-if="!pass&&!weixin">
							<image src="../../static/content/bianjia.png"></image>变价通知我
						</button>
						<view @tap="baoMing(detail.id,'项目落地页+变价通知我',91,'变价通知我',1)" v-if="pass||weixin">
							<image src="../../static/content/bianjia.png"></image>变价通知我
						</view>
					</view>

				</view>
			</view>
		</view>
		<view class="tel_box">
			<view class="left">
				<view class="tel">
					{{telphone}}
				</view>
				<view class="pp">
					致电售楼处了解更多信息
				</view>
			</view>
			<view class="right_btn" @tap="boTel(old_telphone)">
				<image src="../../static/content/botel.png" mode=""></image>
				<text>一键拨打</text>
			</view>

		</view>
		<!-- 优惠信息 -->
		<view class="hui">
			<view class="tit">
				<text>优惠信息</text>
				<image src="../../static/content/wen.png" mode="" @tap="showRules" v-if="!isweixin"></image>
			</view>
			<view class="youhui_01" v-if="!isweixin">
				<text class="text">
					售楼处专供家园客户
					<text class="jie">
						（{{goufang_date}}截止）
					</text>
				</text>
				<view class="right">
					<button class="ling_btn" open-type="getPhoneNumber"
						@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+领取优惠',94,'领取优惠')" hover-class="none"
						v-if="!pass&&!weixin">
						<view>
							立即领
						</view>
					</button>
					<view class="ling_btn" @tap="baoMing(detail.id,'项目落地页+领取优惠',94,'领取优惠',1)" v-if="pass||weixin">
						立即领
					</view>
					<text>{{goufang_ling}}人已领</text>
				</view>
			</view>
			<view class="youhui_02">
				<text class="text">
					免费专车1对1服务限时券
					<text class="jie">
						（剩余{{seefang_sheng}}张）
					</text>
				</text>
				<view class="right">
					<button class="ling_btn" open-type="getPhoneNumber"
						@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+免费领取',95,'免费领取')" hover-class="none"
						v-if="!pass||!weixin">
						<view>
							免费领
						</view>
					</button>
					<view class="ling_btn" @tap="baoMing(detail.id,'项目落地页+免费领取',95,'免费领取',1)" v-if="pass||weixin">
						免费领
					</view>
					<text>{{seefang_ling}}人已领</text>
				</view>
			</view>
		</view>
		<!-- 今日特价房 -->
		<view class="tejia" v-if="tejia.length>0">
			<view class="tit">
				<view class="left">
					<image src="../../static/index/index-bighot.png" mode=""></image>今日特价房
				</view>
				<view class="right">
					距结束仅剩02天
					<view class="time">
						02
					</view>
					<text>:</text>
					<view class="time">
						02
					</view>
					<text>:</text>
					<view class="time">
						02
					</view>
				</view>
			</view>
			<view class="line">
			</view>
			<view class="te-scroll">
				<scroll-view class="scroll-view_H" scroll-x="true">
					<view class="scrollbox" v-for="(item,index) in tableList" :key="item.id">
						<view class="bombest">
						</view>
						<view class="conmsg">
							<text>房号-{{ (item.number).slice(2) }} {{ parseInt(item.area) }}m²</text>
						</view>
						<view class="topmsg">
							<view class="newpri">
								特价<text>{{ ((item.total)/10000).toFixed(1) }}</text>万
							</view>
							<view class="oldpri">
								原价{{ ((item.original_total)/10000).toFixed(1) }}万
							</view>
							<button class="btn" open-type="getPhoneNumber"
								@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+立即抢',93,'立即抢')"
								hover-class="none" v-if="!pass&&!weixin">
								立即抢
							</button>
							<view @tap="baoMing(detail.id,'项目落地页+立即抢',93,'立即抢',1)" class="btn" v-if="pass||weixin">
								立即抢
							</view>
						</view>
					</view>
				</scroll-view>
			</view>
			<view class="xiaoxi">
				<uni-notice-bar :text="specials.dynamic" scrollable showType="slider" background-color="#fff"
					:showIcon="true" color="#646466" :speed="50" v-if="specials.dynamic" :single="true">
				</uni-notice-bar>
			</view>
			<button class="buttons" open-type="getPhoneNumber"
				@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+咨询特价房',93,'咨询特价房')" hover-class="none"
				v-if="!pass&&!weixin">
				咨询特价房
			</button>
			<view @tap="baoMing(detail.id,'项目落地页+咨询特价房',93,'咨询特价房',1)" class="button" v-if="pass||weixin">
				咨询特价房
			</view>
		</view>
		<!-- 主力户型 -->
		<view class="huxing" v-if="goodsList.length>0">
			<view class="tit huxing_tit">
				<view class="left">
					主力户型
				</view>
				<view class="right" @tap="moreHuxing(detail.id)">
					<text>全部户型</text>
					<image src="../../static/content/you.png" mode=""></image>
				</view>
			</view>
			<scroll-view class="floor-list" scroll-x>
				<view class="scoll-wrapper">
					<view v-for="(item, index) in goodsList" :key="index" class="floor-item">
						<navigator :url="`/pages/hudetail/hudetail?id=${item.id}`">
							<view class="bg_image">
								<image :src="item.big" mode="aspectFill"></image>
							</view>
							<view class="bottom">
								<view class="title clamp">{{item.title}}
									<text class="status">{{item.state}}</text>
								</view>
								<view class="area">面积 {{item.area}}m²</view>
								<view class="yue">约
									<text class="num">{{item.price}}</text>
									万/套
								</view>
							</view>
						</navigator>
					</view>
				</view>
			</scroll-view>
			<button open-type="getPhoneNumber"
				@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+领取全部户型资料',97,'领取全部户型资料')" class="hu_zi"
				hover-class="none" v-if="!pass&&!weixin">
				领取全部户型资料
			</button>
			<view class="hu_zi" @tap="baoMing(detail.id,'项目落地页+领取全部户型资料',97,'领取全部户型资料',1)" v-if="pass||weixin">
				领取全部户型资料
			</view>
		</view>
		<!-- 最新动态 -->
		<view class="dongtai">
			<view class="title">
				<view class="dong_left">实时动态<view class="newmsg">新消息<view class="jiao"></view>
					</view>
				</view>
				<view class="dong_right" @tap="allDong(detail.id)">
					全部动态
					<image src="../../static/content/you.png" mode=""></image>
				</view>
			</view>

			<view class="dong_list">
				<view class="dong_one" v-for="(item,key) in dongtai" :key="item.id">
					<view class="round">
					</view>
					<view class="line">
					</view>
					<navigator :url="`/pages/dynamicdetail/dynamicdetail?id=${item.id}`">
						<view class="dong">{{item.introduce}}</view>
						<view class="more" v-if="item.introduce.length>50">
							阅读全文
						</view>
						<view class="time">
							<view class="icon" v-if="key==0">最新动态</view>
							<view class="icon ic1" v-if="key==1">最新加推</view>{{item.time}}
						</view>
					</navigator>
				</view>
				<button class="button" open-type="getPhoneNumber" hover-class="none"
					@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+订阅最新动态',98,'订阅实时动态')" v-if="!pass&&!weixin">
					订阅最新动态
				</button>
				<view class="button" @tap="baoMing(detail.id,'项目落地页+订阅最新动态',98,'订阅实时动态',1)" v-if="pass||weixin">
					订阅最新动态
				</view>
			</view>
		</view>
		<view class="bg_hui"></view>
		<!-- 最新成交价 -->
		<view class="zui_price" v-if="deal_prices.length>0">
			<view class="tit">
				最新成交价
				<view class="cha">
					已有<text>647</text>人查询
				</view>
			</view>
			<!-- <view class="zhu_box">
				<canvas canvas-id="canvasColumn" id="canvasColumn" class="charts" > </canvas>
			</view> -->
			<view class="table_box">
				<view class="table">
					<t-table border=1 class="tables">
						<t-tr class="thead">
							<t-th>日期</t-th>
							<t-th>成交套数</t-th>
							<t-th>成交金额</t-th>
						</t-tr>
						<t-tr v-for="(item,index) in deal_prices" :key="item.id" v-if="index<num2">
							<t-td>{{ item.time }}</t-td>
							<t-td>{{ item.num }}套</t-td>
							<t-td>***万</t-td>
						</t-tr>
					</t-table>
				</view>
				<view class="yincang" @tap="showPrice" v-show="table_show2">
					<image src="../../static/content/xia.png" mode=""></image>
				</view>
			</view>
			<view class="bim">
				<image src="../../static/content/msgicon.png" mode=""></image>
				<swiper class="swiper" :vertical="true" :circular="true" :autoplay="true" interval="2000">
					<swiper-item class="swiper-item" v-for="item in deal_prices" :key="item.id">
						<view class="swiper-item uni-bg-red">客户 {{item.tel}} {{item.time.substr(5)}} 成功购房</view>
					</swiper-item>
				</swiper>
			</view>
			<button open-type="getPhoneNumber" class="get_di_price" hover-class="none"
				@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+获取最新成交价',101,'获取最新成交价')" v-if="!pass&&!weixin">
				获取最新成交价
			</button>
			<view class="get_di_price" @tap="baoMing(detail.id,'项目落地页+获取最新成交价',101,'获取最新成交价',1)" v-if="pass||weixin">
				获取最新成交价
			</view>
		</view>
		<!-- 置业分析报告 -->
		<view class="fenxi">
			<view class="tit">
				置业分析报告
			</view>
			<view class="map">
				<image :src="map_image" v-if="map_image!==''"></image>
			</view>
			<view class="li">
				<view class="title">
					<image src="../static/special/spcial-round.png" mode=""></image>
					投资分析
				</view>
				<view class="licon">
					{{fenxi_tou[0].content}}{{fenxi_tou[1].content}}
				</view>
			</view>
			<view class="li">
				<view class="title">
					<image src="../static/special/spcial-round.png" mode=""></image>
					投资分析
				</view>
				<view class="licon">
					{{fenxi_yiju[0].content}}{{fenxi_yiju[1].content}}
				</view>
			</view>
			<view class="zhaobox">
				<image src="../static/special/spcial-round.png" mode=""></image>
			</view>
			<button class="btn" open-type="getPhoneNumber" hover-class="none"
				@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+领取分析资料',99,'领取分析资料')" v-if="!pass&&!weixin">
				领取分析资料
			</button>
			<view class="btn" @tap="baoMing(detail.id,'项目落地页+领取分析资料',99,'领取分析资料',1)" v-if="pass||weixin">
				领取分析资料
			</view>
		</view>
		<view class="bg_hui"></view>
		<!-- 家园咨询师 -->
		<view class="ye_people">
			<view class="tit">
				允家咨询师
			</view>
			<view class="tese">
				<view class="te_01">
					<image src="../../static/content/zhuan.png" mode=""></image>
					专业服务
				</view>
				<view class="te_02">
					<image src="../../static/content/dingwei.png" mode=""></image>
					区域解读
				</view>
				<view class="te_03">
					<image src="../../static/content/huxing.png" mode=""></image>
					户型分析
				</view>
			</view>

			<view class="ye_one" v-if="staff">
				<image :src="staff.img" mode="" class="head_img"></image>
				<view class="peo">
					<view class="top">
						{{staff.name}}
						<text>满意度{{staff.num}}分</text>
					</view>
					<view class="bottom">
						了解房源特色，专业挑好房
					</view>
				</view>
				<view class="bo_tel">
					<button open-type="getPhoneNumber" hover-class="none"
						@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+咨询服务',104,'咨询服务')" class="bo_zi"
						v-if="!pass&&!weixin">
						<image src="../../static/content/zixun.png" mode=""></image>
					</button>
					<view class="bo_zi" @tap="baoMing(detail.id,'项目落地页+咨询服务',104,'咨询服务',1)" v-if="pass||weixin">
						<image src="../../static/content/zixun.png" mode=""></image>
					</view>
					<image src="../../static/content/tel.png" mode="" @tap="boTel(old_telphone)"></image>
				</view>
			</view>
			<view class="ye_one" v-if="staff">
				<image :src="staff.img" mode="" class="head_img"></image>
				<view class="peo">
					<view class="top">
						{{staff.name}}
						<text>满意度{{staff.num}}分</text>
					</view>
					<view class="bottom">
						为客户提供专业的购房建议
					</view>
				</view>
				<view class="bo_tel">
					<button open-type="getPhoneNumber" hover-class="none"
						@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+咨询服务',104,'咨询服务')" class="bo_zi"
						v-if="!pass&&!weixin">
						<image src="../../static/content/zixun.png" mode=""></image>
					</button>
					<view class="bo_zi" @tap="baoMing(detail.id,'项目落地页+咨询服务',104,'咨询服务',1)" v-if="pass||weixin">
						<image src="../../static/content/zixun.png" mode=""></image>
					</view>
					<image src="../../static/content/tel.png" mode="" @tap="boTel(old_telphone)"></image>
				</view>
			</view>


		</view>
		<!-- 周边配套 -->
		<view class="zhou_pei">
			<view class="zhou">周边配套</view>
			<view class="wei">
				<text class="left">位置:</text>
				<text class="rig">{{detail.address}}</text>
			</view>
			<button open-type="getPhoneNumber" class="pei" hover-class="none"
				@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+获取周边5公里详细配套',102,'获取详细周边配套')"
				v-if="!pass&&!weixin">
				<text>配套:</text>
				咨询具体位置和周边设施情况
				<image src="../../static/content/biao.png" mode=""></image>
			</button>
			<view class="pei" @tap="baoMing(detail.id,'项目落地页+获取周边5公里详细配套',102,'获取详细周边配套',1)" v-if="pass||weixin">
				<text>配套:</text>
				咨询具体位置和周边设施情况
				<image src="../../static/content/biao.png" mode=""></image>
			</view>
			<!-- 周边配套地图 -->
			<view class="scrollbox">
				<scroll-view class="scrolls" scroll-x="true">
					<view class="active">
						公交
					</view>
					<view>
						地铁
					</view>
					<view>
						教育
					</view>
					<view>
						医院
					</view>
					<view>
						购物
					</view>
					<view>
						美食
					</view>
					<view>
						娱乐
					</view>
				</scroll-view>
			</view>
			<view class="address" @tap="goweb">
				<view class="map">
					<image :src="map_image1" v-if="map_image!==''"></image>
				</view>
			</view>
			<button class="button" open-type="getPhoneNumber" hover-class="none"
				@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+获取周边5公里详细配套',102,'获取详细周边配套')"
				v-if="!pass&&!weixin">
				获取周边5公里详细配套
			</button>
			<view class="button" @tap="baoMing(detail.id,'项目落地页+获取周边5公里详细配套',102,'获取详细周边配套',1)" v-if="pass||weixin">
				获取周边5公里详细配套
			</view>
		</view>
		<!-- 楼盘点评 -->
		<view class="lou_dian">
			<view class="top">
				<view class="tit">
					楼盘点评
				</view>
				<view class="other" @click="allDian(detail.id)">
					全部点评
					<image src="../../static/content/right.png" mode=""></image>
				</view>
			</view>
			<view class="bottom">
				<template v-if="comments.length>0">
					<view class="ping_one" v-for="item in comments" :key="item.id">
						<navigator :url="`/pages/diandetail/diandetail?id=${item.id}`">
							<view class="left">
								<image src="../../static/content/ping_img.png" mode=""></image>
							</view>
						</navigator>
						<view class="right">
							<view class="top_tit">
								<navigator :url="`/pages/diandetail/diandetail?id=${item.id}`">
									<text class="tel">{{item.mobile}}</text>
								</navigator>
								<button open-type="getPhoneNumber"
									@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+获取周边5公里详细配套',102,'获取详细周边配套',1,item.id)"
									@tap="did = item.id" v-if="!pass&&!weixin">
									<view class="no_zan" v-if="item.my_like == 0">
										<image src="../../static/content/no_zan.png" mode=""></image>
										赞({{item.like_num}})
									</view>
								</button>
								<view class="no_zan" v-if="item.my_like == 0 && (pass||weixin)" @tap="getLike(item.id)">
									<image src="../../static/content/no_zan.png" mode=""></image>
									赞({{item.like_num}})
								</view>
								<button open-type="getPhoneNumber"
									@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+获取周边5公里详细配套',102,'获取详细周边配套',1,item.id)"
									@tap="did = item.id" v-if="!pass&&!weixin">
									<view class="zan" v-if="item.my_like ==1">
										<image src="../../static/content/zan.png" mode=""></image>
										赞({{item.like_num}})
									</view>
								</button>
								<view class="zan" v-if="item.my_like ==1 && (pass||weixin)" @tap="getLike(item.id)">
									<image src="../../static/content/zan.png" mode=""></image>
									赞({{item.like_num}})
								</view>
							</view>
							<view class="content">
								<navigator :url="`/pages/diandetail/diandetail?id=${item.id}`">
									{{item.content}}
								</navigator>
							</view>
							<view class="time_box">
								<navigator :url="`/pages/diandetail/diandetail?id=${item.id}`">
									<text class="time">{{item.time}}</text>
								</navigator>
								<text class="delete" @tap="deletePing(item.id)"
									v-if="item.mine == true && (pass||weixin)">删除</text>
								<button class="delete" open-type="getPhoneNumber" hover-class="none"
									v-if="item.mine==true && !pass&&!weixin"
									@getphonenumber="getPhoneNumber($event,detail.id,'项目落地页+删除',102,'删除',3,item.id)">删除</button>
							</view>
						</view>

					</view>

				</template>
				<template v-else>
					<view class="zanwu">
						暂无评论，快来评论吧
					</view>
				</template>

			</view>
			<button class="dian_btn" hover-class="none" open-type="getPhoneNumber"
				@getphonenumber="getPhoneNumber($event,detail.id,'跳点评',12,'跳点评',4,detail.id)" v-if="!pass&&!weixin">
				我要点评
			</button>
			<view class="dian_btn" @tap="goDianPing(detail.id)" v-if="pass||weixin">
				我要点评
			</view>
		</view>
		<!-- 楼盘问答 -->
		<view class="lou_wenda">
			<view class="title">
				<text class="title_l">
					楼盘问答
				</text>
				<view class="all_wen" @tap="louwen(detail.id)">
					全部问答
					<image src="../../static/content/right.png" mode=""></image>
				</view>
			</view>

			<view class="wen_list">
				<template v-if="questions.length>0">
					<view class="wen_one" v-for="item in questions" :key="item.id" @tap="gowen(item.id)">
						<!-- <navigator :url="`/pages/wendadetail/wendadetail/?id=${item.id}`"> -->
						<view class="wen_top">
							<text class="wen">问</text>
							<text class="wen_t">{{item.question}}</text>
						</view>
						<view class="da" v-if="item.answer!==''">
							共1个回答
						</view>
						<view class="da" v-else>
							共0个回答
						</view>
						<!-- </navigator> -->
					</view>
				</template>
				<template v-else>
					<view class="zanwu">
						暂无问答，快来提问吧
					</view>
				</template>

				<button class="ti_btn" hover-class="none" open-type="getPhoneNumber"
					@getphonenumber="getPhoneNumber($event,detail.id,'跳提问',13,'跳提问',5,)" v-if="!pass&&!weixin">
					我要提问
				</button>
				<view class="ti_btn" @tap="quTiwen(detail.id)" v-if="pass||weixin">
					我要提问
				</view>
			</view>
		</view>
		<!-- 相关资讯 -->
		<view class="lou_infos" v-if="infos.length>0">
			<view class="infos-tit">
				<text class="kk">相关资讯</text>
				<view class="right" @tap="goloudong">更多资讯<image src="../../static/content/right.png" mode=""></image>
				</view>
			</view>
			<view class="ul">
				<view class="li" v-for="(item,key) in infos" :key="key" v-if="key<=2" @tap="goinfo(item.id)">
					{{item.title}}
				</view>
			</view>
		</view>
		<!-- 看了该楼盘的人还看了 -->
		<view class="about_lou">
			<view class="tit">
				看了该楼盘的人还看了
			</view>
			<view class="pro_list" v-if="false">
				<view class="peo_one" v-for="item in recommends" :key="item.id" @tap="gocon(item.id)">
					<image :src="item.img" mode=""></image>
					<view class="right_pro">
						<view class="pro_name"><text class="name">{{item.name}}</text><text
								class="status">{{item.status}}</text></view>
						<view class="price">{{item.single_price}}元/m²</view>
						<view class="type">
							{{item.type}}<text>|</text>{{item.city}}-{{item.country}}<text>|</text>{{item.area}}m²
						</view>
						<view class="tese">
							<text class="zhuang" v-if="item.decorate">{{item.decorate}}</text>
							<text class="other" v-if="item.railway">{{item.railway}}</text>
							<text class="other" v-for="(ite,index) in item.features" :key="index"
								v-if="index==0">{{ite}}</text>
						</view>
					</view>
				</view>
			</view>
			<pros :recommends="recommends"></pros>
		</view>
		<bottom ref="bomnav" :remark="'项目落地页+预约看房'" :point="103" :title="'预约看房'" :pid="detail.id"
			:telphone="old_telphone"></bottom>
		<wyb-popup ref="popup" type="center" height="750" width="650" radius="12" :showCloseIcon="true"
			@hide="setiscode">
			<sign ref="sign" :type="codenum" @closethis="setpop" :title="title_e" :pid="pid_d" :remark="remark_k"
				:position="position_n" :isok="isok"></sign>
		</wyb-popup>
		<mytoast :txt="msg" ref="msg"></mytoast>
		<!-- 自动获取授权 -->
		<wyb-popup ref="zidong" type="center" height="500" width="660" radius="20" @hide="setiscode">
			<shou @closeshou="clsshou"></shou>
		</wyb-popup>


		<wyb-popup ref="rules" type="center" height="750" width="650" radius="12" :showCloseIcon="true"
			@hide="setiscode">
			<view class="rules">
				<view class="title">
					活动规则
				</view>
				<scroll-view class="text_box" scroll-y="true" :scroll-top="scrollTop">
					<view class="">
						1、本次团购活动以分档累计补发的方案执行，通过允家网站成交该项目具体团购费用如下所示：
					</view>
					<view class="">0-5套---------每套1000元</view>
					<view class="">6-10套--------每套2000元</view>
					<view class="">11-15套-------每套3000元</view>
					<view class="">16-20套-------每套4000元</view>
					<view class="">21套以上------每套5000元</view>
					<view class="">
						2、结算时间：网签成功后次月20号发放。补发费用待该范围内的最后一套网签成功后次月20号发放
					</view>
					<view class="">
						3、核算方式：由开发商或代理公司判定为家园平台客户即可享受这个优惠
					</view>
					<view class="">
						4、结算方式：提供已实名的支付宝账户给与您对接的家园咨询师，规定时间内会将优惠费用打至该账户
					</view>
					<view class="">
						详细活动方案请致电家园客服电话：
					</view>
					<view class="">
						注：活动最终解释权归家园所有
					</view>
				</scroll-view>
			</view>
		</wyb-popup>
		<wyb-popup ref="talkbox" type="bottom" height="440" width="750" radius="0" :showCloseIcon="true"
			@hide="setiscode">
			<view class="talkbox">
				<view class="topbox">
					<view class="imgbos">
						<image :src="staffmsg.staffimg" mode=""></image>
					</view>
					<view class="peoname">
						<view class="name">
							{{staffmsg.staffname}}
							<text>新房咨询</text>
						</view>
						<view class="peonum">
							从业咨询服务6年
						</view>
					</view>
				</view>
				<view class="peomsg">
					<view class="li">
						<view class="litop">
							{{staffmsg.usernum}}<text>人</text>
						</view>
						<view class="libom">
							服务客户
						</view>
					</view>
					<view class="li">
						<view class="litop">
							{{staffmsg.looknum}}<text>次</text>
						</view>
						<view class="libom">
							带看客户
						</view>
					</view>
					<view class="li">
						<view class="litop">
							{{staffmsg.rate}}<text>%</text>
						</view>
						<view class="libom">
							好评率
						</view>
					</view>
				</view>
				<view class="btn">
					<button class="talknow" @tap="nowtal">在线咨询</button>
					<button class="talklate" @tap="late">稍后咨询</button>
				</view>
			</view>
		</wyb-popup>
	</view>
	</view>
</template>

<script>
	import tTable from '@/components/t-table/t-table.vue';
	import tTh from '@/components/t-table/t-th.vue';
	import tTr from '@/components/t-table/t-tr.vue';
	import tTd from '@/components/t-table/t-td.vue';
	import uniNoticeBar from "@/components/uni-notice-bar/uni-notice-bar.vue";
	import uCharts from '@/components/u-charts/u-charts/u-charts.min.js'
	import wybPopup from '@/components/wyb-popup/wyb-popup.vue'
	import sign from '@/components/sign.vue'
	import shou from '@/components/zidong.vue'
	import mytoast from "@/components/mytoast/mytoast.vue"
	import bottom from "@/components/mine/bottom.vue"
	import pros from "@/components/pros.vue"
	var _self;
	var canvaColumn = null;
	export default {
		components: {
			tTable,
			tTh,
			tTr,
			tTd,
			uniNoticeBar,
			sign,
			wybPopup,
			mytoast,
			bottom,
			shou,
			pros
		},
		data() {
			return {
				map_image1: '',
				staffmsg: {},
				pass: false,
				did: 0,
				total: '',
				pro_img: [],
				detail: {},
				tableList: [],
				xiaoxi: '',
				color: '#000',
				goodsList: [],
				dongtai: [],
				num: 3,
				num2: 3,
				table_show: true,
				table_show2: true,
				weixin: false,

				cWidth: '',
				cHeight: '',
				pixelRatio: 1,
				serverData: '',

				class_active: {
					yiju: false,
					active: true,
					yiju2: true,
					active2: false,
				},
				//地图部分
				// latitude: 39.909,
				// longitude: 116.39742,
				map: {},
				covers: [{
					id: 1,
					latitude: "",
					longitude: "",
					iconPath: "../../static/content/map_icon.png",
					width: "30",
					height: "42",
					title: "项目名称",
					label: {
						content: '文本',
						color: '#121212',
						bgColor: '#fff',
						fontSize: 24,
						padding: 40,
						borderWidth: 2,
						borderColor: "#3EACF0",
						borderRadius: 5,
						textAlign: "center"
					},
				}],
				style_list: {
					effect: false,
					e_active: true,
					huxing: true,
					hu_active: false,
				},
				effects: [],
				house_types: [],

				fenxi_data: [],
				fenxi_tou: [],
				fenxi_yiju: [],
				staff: {},
				comments: [],
				questions: [],
				recommends: [],
				common: {},
				telphone: '',
				old_telphone: '',
				specials: {},
				deal_prices: [],
				Column: [],
				tejia: [],
				echarts_year: "",
				project_id: "",

				title_e: '',
				type_e: '',
				pid_d: '',
				remark_k: '',
				position_n: 0,
				codenum: 1,
				msg: "",

				latitude: "",
				longitude: "",
				scrollTop: 0,

				fixed_show: false,

				class_fixed: {
					huxing: true,
					dongtai: false,
					fenxi: false,
					zhou_pei: false
				},

				//优惠信息
				goufang_date: "",
				goufang_ling: "",
				seefang_sheng: "",
				seefang_ling: "",
				pid: 0,
				isok: 0,
				header: {},
				collect: 0,
				shou_old: require("../../static/content/shou.png"),
				has_shou: require("../../static/content/has_shou.png"),
				map_image: "",
				isweixin: false,
				// 资讯
				infos: []
			};
		},
		onLoad(option) {
			if (!uni.getStorageSync('pass')) {
				// this.$refs.zidong.show()
			}
			// this.$refs.talkbox.show()
			// var u = navigator.userAgent;

			// var isbaidu = u.indexOf('baiduboxapp') > -1 ; //百度小程序

			// if( !isbaidu ){
			// 	console.log(879878978979)
			// }else{
			// 	console.log(22223131231)
			// }
			// #ifdef  MP-WEIXIN
			this.isweixin = true
			// #endif
			if (option.other) {
				uni.setStorageSync('other', option.other)
			}
			_self = this;
			this.cWidth = uni.upx2px(750);
			this.cHeight = uni.upx2px(500);
			let id = option.id;
			this.pid = id
			uni.setStorageSync('bid', id)
			this.getdata(id);
			this.pass = uni.getStorageSync('pass')
		},
		onPageScroll(e) {
			if (e.scrollTop >= 200) {
				this.fixed_show = true;
			} else {
				this.fixed_show = false;
			}
		},
		methods: {
			nowtal() {
				this.$refs.talkbox.hide()
				uni.navigateTo({
					url: '/pages/talk/talk?id=' + this.staffmsg.staffid + '&bid=' + this.pid
				})
			},
			late() {
				this.$refs.talkbox.hide()
			},
			goloudong() {
				uni.navigateTo({
					url: '/pages/loudong/loudong?id=' + this.pid + '&type=1'
				})
			},
			goinfo(id) {
				uni.navigateTo({
					url: '/pages/info/info?id=' + id
				})
			},
			gowen(id) {
				uni.navigateTo({
					url: "/pages/wendadetail/wendadetail?id=" + id
				})
			},
			setpop() {
				this.$refs.popup.hide()
			},
			gocon(id) {
				uni.navigateTo({
					url: '/pageA/content/content?id=' + id
				})
			},
			suijiData() {
				let my_date = "";
				let date1 = new Date();
				let date_add_1 = uni.getStorageSync("date_add_1" + this.detail.id);
				if (date_add_1) {
					if ((parseInt(date_add_1)) - (new Date().getTime(new Date())) > 0) {
						let day = uni.getStorageSync("day" + this.detail.id);
						let sheng_num = uni.getStorageSync("sheng_num" + this.detail.id);
						let ling_top = uni.getStorageSync("ling_top" + this.detail.id);
						let ling_bot = uni.getStorageSync("ling_bot" + this.detail.id);
						this.goufang_date = day;
						this.goufang_ling = ling_top;
						this.seefang_sheng = sheng_num;
						this.seefang_ling = ling_bot;
					} else {
						console.log('小于')
						my_date = date1.setDate(date1.getDate() + 1);
						my_date = new Date(my_date);
						uni.setStorageSync("date_add_1" + this.detail.id, my_date.getTime(my_date));

						let date2 = new Date();
						date2.setDate(date2.getDate() + 7);
						let time = date2.getMonth() + 1 + "月" + date2.getDate() + "日";
						uni.setStorageSync("day" + this.detail.id, time)
						//50-100 剩余
						let num = Math.random().toFixed(2) * 50 + 50;
						uni.setStorageSync("sheng_num" + this.detail.id, parseInt(num))
						//100-300 已领
						let ling_top = Math.random().toFixed(2) * 200 + 100;
						uni.setStorageSync("ling_top" + this.detail.id, ling_top)

						let ling_bot = Math.random().toFixed(2) * 200 + 100;
						uni.setStorageSync("ling_bot" + this.detail.id, ling_bot)
						this.goufang_date = time;
						this.goufang_ling = ling_top;
						this.seefang_sheng = parseInt(num);
						this.seefang_ling = ling_bot;
					}
				} else {
					//加一天
					my_date = date1.setDate(date1.getDate() + 1);
					my_date = new Date(my_date);
					uni.setStorageSync("date_add_1" + this.detail.id, my_date.getTime(my_date));

					let date2 = new Date();
					date2.setDate(date2.getDate() + 7);
					let time = date2.getMonth() + 1 + "月" + date2.getDate() + "日";
					uni.setStorageSync("day" + this.detail.id, time)
					//50-100 剩余
					let num = Math.random().toFixed(2) * 50 + 50;
					uni.setStorageSync("sheng_num" + this.detail.id, parseInt(num))
					//100-300 已领
					let ling_top = Math.random().toFixed(2) * 200 + 100;
					uni.setStorageSync("ling_top" + this.detail.id, ling_top)

					let ling_bot = Math.random().toFixed(2) * 200 + 100;
					uni.setStorageSync("ling_bot" + this.detail.id, ling_bot)
					this.goufang_date = time;
					this.goufang_ling = ling_top;
					this.seefang_sheng = parseInt(num);
					this.seefang_ling = ling_bot;
				}

			},
			async getPhoneNumber(e, pid, remark, point, title, type, ping_id) {
				let that = this
				// #ifdef  MP-BAIDU
				if (e.detail.errMsg == 'getPhoneNumber:fail auth deny') {
					if (type) {
						let token = uni.getStorageSync('token');
						if (token) {
							if (type == 1) { //点赞
								this.getLike(ping_id)
							} else if (type == 2) { //收藏
								this.goShou();
							} else if (type == 3) {
								this.deletePing(ping_id);
							} else if (type == 4) { //我要点评
								this.goDianPing(pid)
							} else if (type == 5) { //我要提问
								this.quTiwen(pid)
							}
						} else {
							let url = "/pageA/content/content?id=" + this.detail.id;
							console.log(url);
							uni.setStorageSync("backurl", url)
							uni.navigateTo({
								url: "/pages/login/login"
							})
						}
					} else {
						that.baoMing(pid, remark, point, title, 0)
					}
				} else {
					uni.setStorageSync("pass", true);
					this.pass = true;
					if (type) {
						let token = uni.getStorageSync("token");
						if (token) {
							if (type == 1) { //点赞
								this.getLike(ping_id)
							} else if (type == 2) { //收藏
								this.goShou();
							} else if (type == 3) { //删除
								this.deletePing(ping_id)
							} else if (type == 4) { //我要点评
								this.goDianPing(pid)
							} else if (type == 5) { //我要提问
								this.quTiwen(pid)
							}
						} else {
							let session = uni.getStorageSync('session')
							if (session) {
								uni.request({
									url: 'https://api.edefang.net/applets/baidu/decrypt',
									method: 'get',
									data: {
										iv: e.detail.iv,
										data: e.detail.encryptedData,
										session_key: session,
										other: uni.getStorageSync('other'),
										uuid: uni.getStorageSync('uuid')
									},
									success: (res) => {
										console.log(res, 'session')
										let tel = res.data.mobile
										uni.setStorageSync('phone', tel)
										let openid = uni.getStorageSync('openid')
										uni.request({
											url: "https://api.edefang.net/applets/login",
											method: 'GET',
											data: {
												phone: tel,
												openid: openid,
												other: uni.getStorageSync('other'),
												uuid: uni.getStorageSync('uuid')
											},
											success: (res) => {
												console.log(res)
												uni.setStorageSync('token', res.data.token)
												if (type == 1) { //点赞
													that.getLike(ping_id)
												} else if (type == 2) { //收藏
													that.goShou();
												} else if (type == 3) {
													that.deletePing(ping_id);
												} else if (type == 4) { //我要点评
													that.goDianPing(pid);
												} else if (type == 5) { //我要提问
													that.quTiwen(pid)
												}
											}
										})
									}
								})
							} else {
								console.log(session, "没保存session")
								swan.getLoginCode({
									success: res => {
										// console.log(res.code);
										uni.request({
											url: 'https://api.edefang.net/applets/baidu/get_session_key',
											method: 'get',
											data: {
												code: res.code,
												other: uni.getStorageSync('other'),
												uuid: uni.getStorageSync('uuid')
											},
											success: (res) => {
												console.log(res)
												uni.setStorageSync('openid', res.data.openid)
												uni.setStorageSync('session', res.data
													.session_key)
												uni.request({
													url: "https://api.edefang.net/applets/baidu/decrypt",
													data: {
														data: e.detail.encryptedData,
														iv: e.detail.iv,
														session_key: res.data
															.session_key,
														other: uni.getStorageSync(
															'other'),
														uuid: uni.getStorageSync(
															'uuid')
													},
													success: (res) => {
														console.log(res)
														let tel = res.data.mobile
														uni.setStorageSync('phone',
															tel)
														let openid = uni
															.getStorageSync(
																'openid')
														uni.request({
															url: "https://api.edefang.net/applets/login",
															method: 'GET',
															data: {
																phone: tel,
																openid: openid,
																other: uni
																	.getStorageSync(
																		'other'
																	),
																uuid: uni
																	.getStorageSync(
																		'uuid'
																	)
															},
															success: (
																res
															) => {
																console
																	.log(
																		res
																	)
																uni.setStorageSync(
																	'token',
																	res
																	.data
																	.token
																)
																if (type ==
																	1
																) { //点赞
																	that.getLike(
																		ping_id
																	)
																} else if (
																	type ==
																	2
																) { //收藏
																	that
																		.goShou();
																} else if (
																	type ==
																	3
																) {
																	that.deletePing(
																		ping_id
																	);
																} else if (
																	type ==
																	4
																) { //我要点评
																	that.goDianPing(
																		pid
																	);
																} else if (
																	type ==
																	5
																) { //我要提问
																	that.quTiwen(
																		pid
																	)
																}

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
					} else {
						let session = uni.getStorageSync('session')
						if (session) {
							uni.request({
								url: 'https://api.edefang.net/applets/baidu/decrypt',
								method: 'get',
								data: {
									iv: e.detail.iv,
									data: e.detail.encryptedData,
									session_key: session,
									other: uni.getStorageSync('other'),
									uuid: uni.getStorageSync('uuid')
								},
								success: (res) => {
									console.log(res, 'session')
									let tel = res.data.mobile
									uni.setStorageSync('phone', tel)
									let openid = uni.getStorageSync('openid')
									that.tel = tel;
									that.baoMing(pid, remark, point, title, 1)
								}
							})
						} else {
							console.log(session, "没保存session")
							uni.login({
								provider: 'baidu',
								success: function(res) {
									console.log(res.code);
									uni.request({
										url: 'https://api.edefang.net/applets/baidu/get_session_key',
										method: 'get',
										data: {
											code: res.code,
											other: uni.getStorageSync('other'),
											uuid: uni.getStorageSync('uuid')
										},
										success: (res) => {
											console.log(res)
											uni.setStorageSync('openid', res.data.openid)
											uni.setStorageSync('session', res.data.session_key)
											uni.request({
												url: "https://api.edefang.net/applets/baidu/decrypt",
												data: {
													data: e.detail.encryptedData,
													iv: e.detail.iv,
													session_key: res.data.session_key,
													other: uni.getStorageSync('other'),
													uuid: uni.getStorageSync('uuid')
												},
												success: (res) => {
													console.log(res)
													let tel = res.data.mobile
													uni.setStorageSync('phone',
														tel)
													let openid = uni
														.getStorageSync('openid')
													//	that.$refs.sign.tel = tel
													that.baoMing(pid, remark,
														point, title, 1)
												}
											})
										}
									})
								}
							});
						}
					}
				}
				// #endif
				// #ifdef  MP-WEIXIN
				if (e.detail.errMsg != 'getPhoneNumber:ok') {
					if (type) {
						let token = uni.getStorageSync('token');
						if (token) {
							if (type == 1) { //点赞
								this.getLike(ping_id)
							} else if (type == 2) { //收藏
								this.goShou();
							} else if (type == 3) {
								this.deletePing(ping_id);
							} else if (type == 4) { //我要点评
								this.goDianPing(pid)
							} else if (type == 5) { //我要提问
								this.quTiwen(pid)
							}
						} else {
							let url = "/pageA/content/content?id=" + this.detail.id;
							console.log(url);
							uni.setStorageSync("backurl", url)
							uni.navigateTo({
								url: "/pages/login/login"
							})
						}
					} else {
						that.baoMing(pid, remark, point, title, 0)
					}
				} else {
					if (type) {
						let token = uni.getStorageSync("token");
						if (token) {
							if (type == 1) { //点赞
								this.getLike(ping_id)
							} else if (type == 2) { //收藏
								this.goShou();
							} else if (type == 3) { //删除
								this.deletePing(ping_id)
							} else if (type == 4) { //我要点评
								this.goDianPing(pid)
							} else if (type == 5) { //我要提问
								this.quTiwen(pid)
							}
						} else {
							uni.login({
								provider: 'weixin',
								success: function(res) {
									// console.log(res.code);
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
											uni.setStorageSync('session', res.data.data
												.session_key)
											uni.request({
												url: "https://ll.edefang.net/api/weichat/decryptData",
												data: {
													data: e.detail.encryptedData,
													iv: e.detail.iv,
													sessionKey: res.data.data
														.session_key,
													other: uni.getStorageSync('other'),
													uuid: uni.getStorageSync('uuid')
												},
												success: (res) => {
													console.log(res)
													if (res.code != 500) {
														that.pass = true
														uni.setStorageSync('pass',
															that.pass)
														let data = JSON.parse(res
															.data.message)
														let tel = data
															.purePhoneNumber
														uni.setStorageSync('phone',
															tel)
														let openid = uni
															.getStorageSync(
																'openid')
														uni.request({
															url: "https://api.edefang.net/applets/login",
															method: 'GET',
															data: {
																phone: tel,
																openid: openid,
																other: uni
																	.getStorageSync(
																		'other'
																	),
																uuid: uni
																	.getStorageSync(
																		'uuid'
																	)
															},
															success: (
																res
															) => {
																console
																	.log(
																		res
																	)
																uni.setStorageSync(
																	'token',
																	res
																	.data
																	.token
																)
																if (type ==
																	1
																) { //点赞
																	that.getLike(
																		ping_id
																	)
																} else if (
																	type ==
																	2
																) { //收藏
																	that
																		.goShou();
																} else if (
																	type ==
																	3
																) {
																	that.deletePing(
																		ping_id
																	);
																} else if (
																	type ==
																	4
																) { //我要点评
																	that.goDianPing(
																		pid
																	);
																} else if (
																	type ==
																	5
																) { //我要提问
																	that.quTiwen(
																		pid
																	)
																}

															}
														})
													} else {
														uni.showToast({
															title: "获取失败请重新报名",
															icon: "none"
														})
													}
												}
											})
										}
									})
								}
							});
						}
					} else {
						uni.login({
							provider: 'weixin',
							success: function(res) {
								console.log(res.code);
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
										uni.setStorageSync('session', res.data.data
											.session_key)
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
												console.log(res)
												if (res.data.code != 500) {
													that.pass = true
													uni.setStorageSync('pass', that
														.pass)
													let data = JSON.parse(res.data
														.message)
													let tel = data.purePhoneNumber
													let token = uni.getStorageSync(
														'token')
													if (!token) {
														let openid = uni
															.getStorageSync(
																'openid')
														uni.request({
															url: "https://api.edefang.net/applets/login",
															method: 'GET',
															data: {
																phone: tel,
																openid: openid,
																other: uni
																	.getStorageSync(
																		'other'
																	),
																uuid: uni
																	.getStorageSync(
																		'uuid'
																	)
															},
															success: (
																res
															) => {
																console
																	.log(
																		res
																	)
																uni.setStorageSync(
																	'token',
																	res
																	.data
																	.token
																)
															}
														})
													}
													uni.setStorageSync('phone',
														tel)
													let openid = uni
														.getStorageSync('openid')
													that.baoMing(pid, remark,
														point, title, 1)
												} else {
													uni.showToast({
														title: "获取失败请重新报名",
														icon: "none"
													})
												}
											}
										})
									}
								})
							}
						});
					}
				}
				// #endif
			},
			to(item, num) {
				uni.createSelectorQuery().select(".detail").boundingClientRect(data => { //目标节点
					uni.createSelectorQuery().select("." + item).boundingClientRect((res) => { //最外层盒子节点
						uni.pageScrollTo({
							duration: 0, //过渡时间必须为0，uniapp bug，否则运行到手机会报错
							scrollTop: res.top - data.top - 60, //滚动到实际距离是元素距离顶部的距离减去最外层盒子的滚动距离
						})
					}).exec()
				}).exec();
				if (num == 1) {
					this.class_fixed.huxing = true;
					this.class_fixed.dongtai = false;
					this.class_fixed.fenxi = false;
					this.class_fixed.zhou_pei = false;
				} else if (num == 2) {
					this.class_fixed.huxing = false;
					this.class_fixed.dongtai = true;
					this.class_fixed.fenxi = false;
					this.class_fixed.zhou_pei = false;
				} else if (num == 3) {
					this.class_fixed.huxing = false;
					this.class_fixed.dongtai = false;
					this.class_fixed.fenxi = true;
					this.class_fixed.zhou_pei = false;
				} else if (num == 4) {
					this.class_fixed.huxing = false;
					this.class_fixed.dongtai = false;
					this.class_fixed.fenxi = false;
					this.class_fixed.zhou_pei = true;
				}
			},
			goZhou(id) {
				uni.navigateTo({
					url: "/pages/aroundweb/aroundweb?id=" + id
				})
			},
			showRules() {
				this.$refs.rules.show();
			},
			getLike(id) {
				let token = uni.getStorageSync("token");
				if (token) {
					uni.request({
						url: this.apiserve + "/comment/like",
						data: {
							token: token,
							id: id,
							other: uni.getStorageSync('other'),
							uuid: uni.getStorageSync('uuid')
						},
						header: {
							'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
						},
						method: "POST",
						success: (res) => {
							if (res.data.code == 200) {
								this.$refs.msg.show();
								this.msg = res.data.message;
								this.getdata(this.detail.id)
							}
						}

					})
				} else {
					this.$refs.msg.show();
					this.msg = "请先登录"
					let url = "/pageA/content/content?id=" + this.detail.id;
					uni.setStorageSync("backurl", url)
					uni.navigateTo({
						url: "/pages/login/login"
					})

				}
			},
			deletePing(id) {
				let token = uni.getStorageSync("token");
				if (token) {
					uni.request({
						url: this.apiserve + '/comment/delete',
						method: "POST",
						data: {
							token: token,
							id: id,
							other: uni.getStorageSync('other'),
							uuid: uni.getStorageSync('uuid')
						},
						header: {
							'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
						},
						success: (res) => {
							if (res.data.code == 200) {
								this.$refs.msg.show();
								this.msg = res.data.message;
								this.getdata(this.detail.id)
							} else {
								console.log(res)
							}
						},
						fail: (err) => {
							console.log(err)
						}

					})
				} else {
					this.$refs.msg.show();
					this.msg = "请先登录"
					let url = "/pageA/content/content?id=" + this.detail.id;
					uni.setStorageSync("backurl", url)
					uni.navigateTo({
						url: "/pages/login/login"
					})
				}
			},
			getdata(id) {
				uni.showLoading({
					title: "加载中"
				})
				let ip = '';
				let other = uni.getStorageSync("other");
				let token = uni.getStorageSync("token");
				uni.request({
					url: this.putserve + "/getIp.php",
					method: "GET",
					success: (res) => {
						ip = res.data
						ip = ip.split('=')[1].split(':')[1]
						ip = ip.substring(1)
						ip = ip.slice(0, -3)
						uni.request({
							url: this.apiserve + '/applets/building/detail',
							data: {
								id: id,
								other: other,
								ip: ip,
								token: token,
								other: uni.getStorageSync('other'),
								uuid: uni.getStorageSync('uuid')
							},
							success: (res) => {
								if (res.data.code == 200) {
									console.log(res, "res");
									let data = res.data.data;
									this.pro_img = data.imgs.img.effects;

									this.effects = data.imgs.img.effects;
									this.house_types = data.imgs.img.house_types;

									this.total = data.imgs.num;
									this.detail = data.abstract;
									uni.setNavigationBarTitle({
										title: data.abstract.name
									});
									this.goodsList = data.house_types;
									this.dongtai = data.dynamics;
									this.staff = data.staff;
									this.comments = data.comments;
									this.questions = data.questions;
									this.recommends = data.recommends;
									this.common = data.common;
									this.header = data.common.header;
									this.collect = data.collect;

									this.latitude = data.abstract.latitude;
									this.longitude = data.abstract.longitude;
									this.covers[0].latitude = data.abstract.latitude;
									this.covers[0].longitude = data.abstract.longitude;
									// this.covers[0].width = 280;
									// this.covers[0].height = 72;
									this.covers[0].title = data.abstract.name;
									this.covers[0].label.content = data.abstract.name;
									let lng = data.abstract.longitude;
									let lat = data.abstract.latitude;
									let name = data.abstract.name;
									console.log(name);
									let img_map = require('../../static/content/map_icon.png');
									this.map_image =
										`http://api.map.baidu.com/staticimage/v2?ak=Tz47quqSiGkQi7RyS3QKaFZxMy3GbH5o&center=${lng},${lat}&width=315&height=130&zoom=17&markers=${lng},${lat}`;
									this.map_image1 =
										`http://api.map.baidu.com/staticimage/v2?ak=Tz47quqSiGkQi7RyS3QKaFZxMy3GbH5o&center=${lng},${lat}&width=315&height=150&zoom=17&markers=${lng},${lat}`;

									this.suijiData();


									console.log(this.covers, 'covers');

									let phone = data.common.phone;
									this.telphone = phone.replace(',', '转');
									this.old_telphone = phone;
									this.specials = data.specials;
									let tejia = data.specials.data;
									if (tejia == null) {
										this.tejia = [];
									} else {
										this.tejia = data.specials.data;
									}

									this.deal_prices = data.deal_prices;
									console.log(this.telphone);

									let _self = this;

									let arr_data = data.deal_prices;
									let time = [];
									let num = [];
									arr_data.map(n => {
										num.push(n.price);
										let str = n.time.substring(n.time.length - 5);
										let strr = str.replace("-", '.');
										time.push(strr);
										let year = n.time.substring(0, 4);
										this.echarts_year = year;
									})


									let Column = {
										categories: [],
										series: []
									};
									Column.series = [{
										"name": this.echarts_year + "年",
										"textColor": "#fff",
										"data": num,
									}, ];

									Column.categories = time;
									this.Column = Column;
									console.log(Column, 'Column');
									_self.showColumn("canvasColumn", Column);



									let arr = data.specials.data;
									if (arr) {
										arr.map(p => {
											let str = p.diff.toString();
											p.diff = str.substring(0, str.length - 2) +
												'**'
										})
										this.tableList = arr;
									}

									let analysis = data.analysis;
									let fenxi_tou = [];
									let fenxi_yiju = [];
									analysis.map(m => {
										if (m.type == 1) { //投资分析
											fenxi_tou.push(m);
										} else if (m.type == 2) { //宜居分析
											fenxi_yiju.push(m)
										}
									})

									this.fenxi_data = fenxi_tou;
									this.fenxi_tou = fenxi_tou;
									this.fenxi_yiju = fenxi_yiju;

									// #ifdef MP-BAIDU
									let header = data.common.header;
									let img = data.imgs.img.effects;
									let arrs = [];
									if (img.length > 0) {
										img.map(p => {
											arrs.push(p.small)
										})
									}
									swan.setPageInfo({
										title: header.title,
										keywords: header.keywords,
										description: header.description,
										image: arrs,
										success: res => {
											console.log('setPageInfo success', res);
										},
										fail: err => {
											console.log('setPageInfo fail', err);
										}
									})
									// #endif
									this.infos = data.article
									uni.hideLoading()

								}
							}
						})
					}
				})

			},
			goShou() {
				let token = uni.getStorageSync('token');
				if (token) {
					uni.request({
						url: this.apiserve + "/jy/collect",
						method: "POST",
						data: {
							token: token,
							id: this.detail.id,
							type: 1,
							other: uni.getStorageSync('other'),
							uuid: uni.getStorageSync('uuid')
						},
						header: {
							'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
						},
						success: (res) => {
							if (res.data.code == 200) {
								this.$refs.msg.show();
								this.msg = res.data.message;
								this.getdata(this.detail.id)
							} else if (res.data.code == 500) {
								this.$refs.msg.show();
								this.msg = res.data.msg;
							}
						}

					})
				} else {
					let url = "/pageA/content/content?id=" + this.detail.id;
					this.$refs.msg.show();
					this.msg = "请先登录"
					uni.setStorageSync("backurl", url)
					uni.navigateTo({
						url: "/pages/login/login"
					})
				}


			},
			quTiwen(id) {
				//先判断登陆了，再跳转
				let url = "/pageA/content/content?id=" + this.detail.id;
				uni.setStorageSync("backurl", url)
				let token = uni.getStorageSync('token');
				if (token) {
					uni.navigateTo({
						url: "/pages/tiwen/tiwen?id=" + id
					})
				} else {
					let url = "/pageA/content/content?id=" + this.detail.id;
					this.msg = "请先登录";
					this.$refs.msg.show();
					uni.setStorageSync("backurl", url)
					uni.navigateTo({
						url: "/pages/login/login"
					})
				}

			},
			goDianPing(id) {
				//先判断登陆了，再跳转
				let url = "/pageA/content/content?id=" + this.detail.id;
				uni.setStorageSync("backurl", url)
				let token = uni.getStorageSync("token");
				if (token) {
					uni.navigateTo({
						url: "/pages/senddian/senddian?id=" + id
					})
				} else {
					let url = "/pageA/content/content?id=" + this.detail.id;
					this.msg = "请先登录";
					this.$refs.msg.show();
					uni.setStorageSync("backurl", url)
					uni.navigateTo({
						url: "/pages/login/login"
					})
				}
			},
			goDuibi(id) {
				uni.navigateTo({
					url: `/pages/loupk/loupk?ids=${id}`
				})
			},
			setiscode() {
				this.codenum = 0
			},
			boTel(tel) {
				uni.makePhoneCall({
					phoneNumber: tel,
					success: function() {
						console.log('拨打电话');
					} //仅为示例
				});
			},
			baoMing(pid, msg, point, title, n) {
				this.isok = n
				this.pid_d = pid;
				this.position_n = point,
					this.title_e = title;
				this.remark_k = msg;
				this.$refs.popup.show();
			},
			showTable() {
				this.num = this.tableList.length;
				this.table_show = false;
			},
			showPrice() {
				this.num2 = this.deal_prices.length;
				this.table_show2 = false;
			},
			showColumn(canvasId, chartData) {
				console.log("111", chartData.categories, chartData.series)
				canvaColumn = new uCharts({
					$this: _self,
					canvasId: canvasId,
					type: 'column',
					legend: true,
					fontSize: 11,
					background: '#FFFFFF',
					pixelRatio: _self.pixelRatio,
					animation: true,
					categories: chartData.categories,
					series: chartData.series,
					xAxis: {
						disableGrid: true,
						rotateLabel: true
					},
					yAxis: {
						data: [{
							format: val => {
								return val + " w"
							}
						}]
					},
					dataLabel: true,
					width: _self.cWidth * _self.pixelRatio,
					height: _self.cHeight * _self.pixelRatio,
					padding: [0, 30, 0, 0],
					extra: {
						column: {
							width: _self.cWidth * _self.pixelRatio * 0.45 / chartData.categories.length
						}
					}
				});
			},
			clsshou(e) {
				console.log(e)
				if (e) {
					this.pass = true
					this.$refs.bomnav.pass = true
				}
				this.$refs.zidong.hide()
			},
			changeData() {
				canvaColumn.updateData({
					series: _self.serverData.ColumnB.series,
					categories: _self.serverData.ColumnB.categories
				});
			},
			showEffect() {
				this.style_list.e_active = true;
				this.style_list.hu_active = false;
				this.pro_img = this.effects;
			},
			showHuxing() {
				this.style_list.e_active = false;

				this.style_list.hu_active = true;
				this.pro_img = this.house_types;
			},
			goDetail() {
				let id = this.detail.id;
				uni.navigateTo({
					url: "/pages/prodetail/prodetail?id=" + id
				})
			},
			moreHuxing(id) {
				uni.navigateTo({
					url: "/pages/prohuxing/prohuxing?id=" + id
				})
			},
			allDong(id) {
				uni.navigateTo({
					url: "/pages/loudong/loudong?id=" + id + '&type=0'
				})
			},
			allDian(id) {
				uni.navigateTo({
					url: "/pages/loudian/loudian?id=" + id
				})
			},
			louwen(id) {
				uni.navigateTo({
					url: "/pages/allwenda/allwenda?id=" + id
				})
			},
			goweb() {
				let id = this.detail.id;
				uni.navigateTo({
					url: "/pages/test/test?id=" + id
				})
			}
		},
	}
</script>

<style lang="less" scoped>
	button {
		padding: 0;
	}

	button::after {
		border: none;
	}

	/deep/ .marker-route {
		position: relative;
		width: 150px;
		height: 34px;
		background: rgba(255, 255, 255, 1);
		border-radius: 3px;
		font-size: 16px;
		font-family: "Microsoft YaHei";
		font-weight: bold;
		color: rgba(51, 51, 51, 1);
		line-height: 34px;
		text-align: center;

		.box_b {
			position: absolute;
			width: 29px;
			height: 15px;
			background: rgba(42, 198, 110, 0.2);
			border-radius: 50%;
			left: 50%;
			margin-left: -15px;

			.box_c {
				position: absolute;
				width: 16px;
				height: 7px;
				background: #2ac66e;
				border-radius: 50%;
				top: 4px;
				left: 6px;
				z-index: 100;
			}
		}
	}

	.detail {
		background-color: #F5F5F5;
		padding-bottom: 128rpx;

		.toptitle {
			color: #17181A;
			font-size: 29.88rpx;
			padding: 0 29.88rpx;
			line-height: 87.64rpx;
			background-color: #fff;
			position: fixed;
			top: 0rpx;
			z-index: 3000;
			width: 100%;

			.status_bar {
				height: var(--status-bar-height);
				width: 100%;
			}

			.nav_top {
				display: inline-block;

				image {
					width: 31.87rpx;
					height: 31.87rpx;
					margin-right: 11.95rpx;
					margin-bottom: -3.98rpx;
				}

				text {
					width: 221rpx;
					font-size: 32rpx;
					font-weight: 500;
					color: #17181A;
				}
			}
		}

		.fixed_top {
			width: 100%;
			height: 88rpx;
			background: #FFFFFF;
			display: flex;
			justify-content: space-around;
			position: fixed;
			z-index: 2000;

			view {
				font-size: 30rpx;
				font-weight: 500;
				color: #323233;
				line-height: 88rpx;
			}

			.active {
				padding-bottom: 16rpx;
				border-bottom: 4rpx solid #3EACF0;
			}
		}

		.lunbo {
			width: 100%;
			height: 420rpx;
			position: relative;

			.swiper {
				width: 100%;
				height: 420rpx;

				image {
					width: 100%;
					height: 420rpx;
				}
			}

			.topimgbtn {
				width: 276rpx;
				border-radius: 20rpx;
				background: rgba(0, 0, 0, 0.6);
				position: absolute;
				bottom: 50rpx;
				left: 237rpx;
				z-index: 10;
				display: flex;
				align-items: center;

				text {
					width: 92rpx;
					height: 40rpx;
					font-size: 20rpx;
					color: #FFFFFF;
					line-height: 40rpx;
					text-align: center;
					display: inline-block;
				}

				.active {
					border-radius: 20rpx;
					background: #2AC66D;
				}
			}

			.shou {
				position: absolute;
				top: 30rpx;
				right: 30rpx;
				width: 48rpx;
				height: 48rpx;

				image {
					width: 100%;
					height: 100%;
				}
			}

			.total {
				width: 100rpx;
				height: 40rpx;
				background: rgba(0, 0, 0, 0.6);
				border-radius: 20px;
				color: #fff;
				font-size: 20rpx;
				position: absolute;
				bottom: 50rpx;
				right: 30rpx;
				text-align: center;
				line-height: 40rpx;
			}
		}

		.name_box {
			margin: 0 30rpx;
			position: relative;
			top: -30rpx;
			height: 667rpx;
			background-color: #FFFFFF;
			padding: 0 30rpx;
			border-radius: 16rpx;

			.top {
				width: 100%;
				height: 175rpx;

				.top_name {
					float: left;
					width: 74%;
					height: 175rpx;

					.name {
						font-size: 40rpx;
						font-weight: 800;
						color: #17181A;
						width: 100%;
						line-height: 40rpx;
						margin-top: 50rpx;
						margin-bottom: 18rpx;
					}

					.tese {
						.one {
							width: 68rpx;
							height: 36rpx;
							background: #E6FAF0;
							border-radius: 6rpx;
							font-size: 22rpx;
							font-weight: 500;
							color: #20C657;
							text-align: center;
							line-height: 36rpx;
							margin-right: 12rpx;
							display: inline-block;
						}

						.other {
							background-color: #F5F5F5;
							color: #7D7E80;
							font-size: 22rpx;
							line-height: 36rpx;
							padding-left: 12rpx;
							padding-right: 12rpx;
							margin-right: 12rpx;
							border-radius: 6rpx;
							height: 36rpx;
							padding-top: 4rpx;
							padding-bottom: 4rpx;
						}
					}
				}

				.right_gong {
					position: absolute;
					top: 52rpx;
					right: 30rpx;

					.duibi {
						width: 132rpx;
						display: flex;
						align-items: center;

						image {
							width: 32rpx;
							height: 32rpx;
							margin-right: 4rpx;
						}

						text {
							font-size: 24rpx;
							font-weight: 500;
							color: #628BB9;
						}
					}
				}

			}

			.huiimg {
				height: 74rpx;
				border-radius: 12rpx;
				position: relative;
				margin-bottom: 26rpx;

				.btn {
					color: #795636;
					font-size: 28rpx;
					position: absolute;
					right: 18rpx;
					top: 16rpx;
					padding-left: 38rpx;
					border-left: 1rpx dashed #C3A26D;

					image {
						width: 28rpx;
						height: 28rpx;
						margin-left: 4rpx;
						margin-bottom: -4rpx;
					}
				}
			}

			.detail_list {
				width: 100%;

				.list_top {
					width: 100%;
					height: 265rpx;

					.list_left {
						float: left;

						.price {
							line-height: 58rpx;

							.left {
								font-size: 28rpx;
								font-weight: 500;
								color: #7D7D80;
								margin-right: 12rpx;
							}

							.strong_price {
								font-size: 40rpx;
								font-weight: 800;
								color: #FF6040;
							}

							.dan {
								font-size: 20rpx;
								font-weight: bold;
								color: #FF6040;
							}
						}

						.kai_time {
							line-height: 58rpx;

							.left {
								font-size: 28rpx;
								font-weight: 500;
								color: #7D7D80;
								margin-right: 12rpx;
							}

							.com {
								font-size: 28rpx;
								font-weight: 500;
								color: #303133;
							}
						}

						.address {
							line-height: 58rpx;
							width: 500rpx;
							overflow: hidden;
							text-overflow: ellipsis;
							white-space: nowrap;

							.left {
								font-size: 28rpx;
								font-weight: 500;
								color: #7D7D80;
								margin-right: 12rpx;
							}

							.com {
								font-size: 28rpx;
								font-weight: 500;
								color: #303133;
							}
						}

						.zhu {
							margin-top: 20rpx;
							margin-bottom: 35rpx;

							text {
								font-size: 24rpx;
								font-weight: 500;
								color: #303233;
								line-height: 24rpx;
							}
						}
					}

					.ref_nav {
						position: absolute;
						right: 30rpx;
						top: 320rpx;

						text {
							font-size: 26rpx;
							font-weight: 500;
							color: #969699;
						}

						.nav {
							width: 24rpx;
							height: 24rpx;
						}
					}

					.right_nav {
						position: absolute;
						right: 30rpx;
						top: 410rpx;
						width: 100rpx;
						height: 64rpx;
					}
				}

				.baoming_btn {
					width: 100%;
					padding-top: 20rpx;

					.btn_box:after {
						content: '';
						overflow: hidden;
						height: 0;
						clear: both;
						visibility: hidden;
						display: block;
					}

					.btn_box {
						width: 100%;
						height: 80rpx;
						margin-bottom: 40rpx;
						display: flex;
						justify-content: space-between;

						button {
							width: 304rpx;
							height: 80rpx;
							background: #E9F5EE;
							border-radius: 12rpx;
							border: none;
							float: left;
							font-size: 30rpx;
							font-weight: bold;
							color: #38916C;
							display: flex;
							justify-content: center;
							align-items: center;

							image {
								width: 36rpx;
								height: 36rpx;
								margin-right: 7rpx;
							}
						}

						view {
							width: 304rpx;
							height: 80rpx;
							background: #E9F5EE;
							border-radius: 12rpx;
							border: none;
							float: left;
							font-size: 30rpx;
							font-weight: bold;
							color: #38916C;
							display: flex;
							justify-content: center;
							align-items: center;

							image {
								width: 36rpx;
								height: 36rpx;
								margin-right: 7rpx;
							}
						}

						.btn01 {
							margin-right: 22rpx;
						}
					}
				}
			}

		}

		.tel_box {
			margin: 0 30rpx;
			height: 128rpx;
			margin-left: 30rpx;
			position: relative;
			background: url(../../static/content/tel_bg.png) no-repeat;
			background-size: 690rpx 128rpx;
			padding-left: 24rpx;
			padding-right: 20rpx;

			.left {
				width: 474rpx;
				float: left;
				padding-top: 32rpx;

				.tel {
					font-size: 36rpx;
					font-weight: bold;
					color: #4C726C;
					line-height: 36rpx;
				}

				.pp {
					font-size: 24rpx;
					font-weight: 400;
					color: #68938C;
					margin-top: 8rpx;
				}
			}

			.right_btn {
				width: 172rpx;
				height: 62rpx;
				background: #FFFFFF;
				border-radius: 31px;
				float: right;
				margin-top: 35rpx;
				display: flex;
				justify-content: center;
				align-items: center;

				image {
					width: 32rpx;
					height: 32rpx;
				}

				text {
					font-size: 26rpx;
					font-weight: 400;
					color: #38916C;
					line-height: 62rpx;
					text-align: center;
				}
			}
		}

		.bg_hui {
			width: 100%;
			height: 20rpx;
			background: #F7F7F7;
		}

		//  优惠信息 
		.hui {
			margin: 0 30rpx;
			// height: 475rpx;
			background: #F7F7F7;
			padding-left: 30rpx;
			padding-right: 30rpx;
			box-sizing: border-box;
			background-color: #fff;
			padding-bottom: 40rpx;
			margin-bottom: 30rpx;

			.tit {
				text {
					font-size: 34rpx;
					font-weight: 800;
					color: #19191A;
					line-height: 114rpx;
				}

				image {
					width: 32rpx;
					height: 32rpx;
					margin-left: 15rpx;
				}
			}

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
						width: 128rpx;
						height: 48rpx;
						background: linear-gradient(270deg, #FF7519, #FFAE3D);
						border-radius: 24rpx;
						font-size: 24rpx;
						font-weight: 500;
						color: #FFFFFF;
						text-align: center;
						line-height: 48rpx;
						position: absolute;
						top: 28rpx;
						right: 30rpx;
					}

					text {
						font-size: 24rpx;
						font-weight: 500;
						color: #FF7519;
						position: absolute;
						right: 44rpx;
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
					color: #3A80BA;
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
						width: 128rpx;
						height: 48rpx;
						background: linear-gradient(270deg, #348AFF, #6ACCFF);
						border-radius: 24rpx;
						font-size: 24rpx;
						font-weight: 500;
						color: #FFFFFF;
						text-align: center;
						line-height: 48rpx;
						position: absolute;
						top: 28rpx;
						right: 30rpx;
					}

					text {
						font-size: 24rpx;
						font-weight: 500;
						color: #40A2F4;
						position: absolute;
						right: 44rpx;
						bottom: 19rpx;
					}
				}
			}
		}

		//  今日特价房 
		.tejia {
			margin: 0 30rpx;
			height: auto;
			background: #fff;
			padding-bottom: 40rpx;
			border-radius: 16rpx;
			margin-bottom: 30rpx;

			.tit {
				width: 100%;
				padding-left: 30rpx;
				padding-right: 30rpx;
				box-sizing: border-box;
				height: 106rpx;

				.left {
					font-size: 34rpx;
					font-weight: 800;
					color: #121212;
					line-height: 106rpx;
					float: left;

					image {
						width: 32rpx;
						height: 32rpx;
					}
				}

				.right {
					font-size: 26rpx;
					font-weight: 500;
					color: #313233;
					line-height: 106rpx;
					float: right;
					display: flex;
					align-items: center;

					.time {
						width: 32rpx;
						height: 32rpx;
						border-radius: 4rpx;
						text-align: center;
						line-height: 32rpx;
						background-color: #FF4040;
						font-size: 22rpx;
						color: #FFFFFF;
					}

					.time:nth-of-type(1) {
						margin-left: 10rpx;
					}

					text {
						font-size: 20rpx;
						color: #FF2F51;
						margin: 0 6rpx;
					}
				}
			}

			.line {
				width: 630rpx;
				margin-left: 30rpx;
				background-color: #EDEDED;
				height: 1rpx;
				margin-bottom: 40rpx;
			}

			.te-scroll {
				white-space: nowrap;

				.scrollbox {
					display: inline-block;
					margin-right: 20rpx;
					width: 280rpx;
					height: 225rpx;
					border-radius: 16rpx;
					position: relative;

					.bombest {
						width: 280rpx;
						height: 160rpx;
						border-radius: 16rpx;
						background: linear-gradient(90deg, #F2444A, #FA8370);
						position: absolute;
						top: 30rpx;
						left: 0;
					}

					.conmsg {
						width: 240rpx;
						height: 156rpx;
						border-radius: 16rpx;
						border: 3rpx solid #ECE3D5;
						text-align: center;
						position: absolute;
						background-color: #FFFFFF;
						top: 0;
						left: 20rpx;

						text {
							color: #69513D;
							font-size: 22rpx;
							padding-top: 22rpx;
						}
					}

					.topmsg {
						width: 280rpx;
						height: 160rpx;
						background: linear-gradient(-90deg, #F2444A 0%, #FA8370 100%);
						border-radius: 0 0 16rpx 16rpx;
						position: absolute;
						bottom: 0;
						left: 0;
						display: flex;
						flex-direction: column;
						align-items: center;

						.newpri {
							color: #FFFFFF;
							font-size: 20rpx;
							margin-bottom: 0rpx;
							padding-top: 12rpx;

							text {
								font-size: 44rpx;
								font-weight: bold;
							}
						}

						.oldpri {
							color: #FAD9D4;
							font-size: 22rpx;
							text-decoration: line-through;
							margin-bottom: 6rpx;
						}

						.btn {
							width: 132rpx;
							height: 40rpx;
							border-radius: 20rpx;
							text-align: center;
							line-height: 40rpx;
							background-color: #FFFFFF;
							color: #7F5431;
							font-size: 24rpx;
						}
					}
				}

				.scrollbox:nth-of-type(1) {
					margin-left: 30rpx;
				}
			}


			.xiaoxi {
				// width: 100%;
				padding: 0 26rpx;
				height: 36rpx;
				margin-top: 37rpx;

				.semp-notice-bar {
					width: 100%;
					padding-left: 26rpx;
					height: 36rpx;
				}
			}

			.buttons {
				width: 630rpx;
				height: 80rpx;
				background: #E9F5EE;
				font-size: 30rpx;
				font-weight: 800;
				color: #38916C;
				text-align: center;
				line-height: 80rpx;
				margin-left: 30rpx;
				margin-top: 30rpx;
			}

			.button {
				width: 630rpx;
				height: 80rpx;
				background: #E9F5EE;
				font-size: 30rpx;
				font-weight: 800;
				color: #38916C;
				text-align: center;
				line-height: 80rpx;
				margin-left: 30rpx;
				margin-top: 30rpx;
			}

		}

		//  主力户型 
		.huxing {
			height: 646rpx;
			margin: 0 30rpx;
			background-color: #fff;
			border-radius: 16rpx;
			margin-bottom: 30rpx;

			.tit {
				line-height: 110rpx;
				padding-left: 30rpx;
				padding-right: 30rpx;
				margin-bottom: 4px;
				height: 110rpx;

				.left {
					font-size: 34rpx;
					font-weight: 800;
					color: #19191A;
					float: left;
				}

				.right {
					float: right;

					text {
						font-size: 26rpx;
						font-weight: 500;
						color: #969699;
					}

					image {
						width: 24rpx;
						height: 24rpx;
					}
				}
			}

			.floor-list {
				white-space: nowrap;

				.scoll-wrapper {
					padding-left: 30rpx;
				}

				.scoll-wrapper {
					display: flex;
					align-items: flex-start;

					.floor-item {
						width: 320rpx;
						height: 379rpx;
						margin-right: 20rpx;

						.bg_image {
							width: 320rpx;
							height: 214rpx;
							background: #F5F5F5;
							border: 1rpx solid #EDEDED;
							border-radius: 12rpx 12rpx 0rpx 0rpx;

							image {
								width: 164rpx;
								height: 211rpx;
								margin-left: 80rpx;
							}
						}

						.bottom {
							width: 297rpx;
							height: 160rpx;
							border: 1rpx solid #EDEDED;
							border-top: none;
							border-radius: 0rpx 0rpx 12rpx 12rpx;
							padding-left: 23rpx;

							.title {
								padding-top: 16rpx;
								font-size: 28rpx;
								font-weight: 800;
								color: #323233;
								line-height: 42rpx;

								// margin-top: 5rpx;
								.status {
									width: 54rpx;
									height: 28rpx;
									background: #29CC72;
									border-radius: 6rpx;
									font-size: 20rpx;
									font-weight: 500;
									color: #FFFFFF;
									line-height: 28rpx;
									margin-left: 24rpx;
									display: inline-block;
									text-align: center;
								}
							}

							.area {
								font-size: 24rpx;
								font-weight: 500;
								color: #7D7E80;
								line-height: 42rpx;
							}

							.yue {
								font-size: 20rpx;
								font-weight: 500;
								color: #FF6040;
								line-height: 42rpx;

								.num {
									font-size: 32rpx;
									font-weight: 800;
									color: #FF6040;
								}
							}
						}

					}

				}
			}

			.hu_zi {
				width: 630rpx;
				height: 80rpx;
				background: #E9F5EE;
				border-radius: 12rpx;
				font-size: 30rpx;
				font-weight: 800;
				color: #38916C;
				text-align: center;
				line-height: 80rpx;
				margin-top: 40rpx;
				margin-left: 30rpx;
			}
		}

		//  最新动态 
		.dongtai {
			height: auto;
			margin: 0 30rpx;
			padding-left: 30rpx;
			padding-right: 30rpx;
			box-sizing: border-box;
			background-color: #fff;
			padding-bottom: 40rpx;
			border-radius: 16rpx;

			.title {
				width: 100%;
				margin-top: 10rpx;
				height: 94rpx;

				.dong_left {
					font-size: 34rpx;
					font-weight: bold;
					color: #121212;
					float: left;
					line-height: 94rpx;
					position: relative;

					.newmsg {
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
						top: 20rpx;

						.jiao {
							position: absolute;
							border: 10rpx solid rgba(0, 0, 0, 0);
							border-left-color: #FF4040;
							left: 0;
							bottom: -8rpx;
						}
					}
				}

				.dong_right {
					font-size: 26rpx;
					font-weight: 500;
					color: #969699;
					float: right;
					line-height: 94rpx;

					image {
						width: 24rpx;
						height: 24rpx;
						margin-left: 12rpx;
					}
				}
			}

			.dong_list {
				padding-left: 10rpx;

				.dong_one {
					padding-left: 29rpx;
					position: relative;
					border-left: 1rpx solid #DFE1E6;
					padding-bottom: 40rpx;

					.round {
						position: absolute;
						width: 20rpx;
						height: 20rpx;
						border-radius: 50%;
						background-color: #DFE1E6;
						left: -10rpx;
						top: 0;
					}

					.dong {
						font-size: 28rpx;
						font-weight: 500;
						color: #646466;
						line-height: 44rpx;
						display: -webkit-box;
						-webkit-box-orient: vertical;
						-webkit-line-clamp: 2;
						overflow: hidden;
						margin-bottom: 18rpx;
					}

					.more {
						width: 150rpx;
						height: 36rpx;
						background: linear-gradient(270deg, #FFFFFF, rgba(0, 0, 0, 0));
						text-align: right;
						line-height: 36rpx;
						position: absolute;
						right: 0;
						top: 46rpx;
						color: #6796CA;
						font-size: 28rpx;
					}

					.time {
						display: flex;
						justify-content: space-between;
						align-items: center;
						font-size: 24rpx;
						color: #AFAFB3;

						.icon {
							width: 110rpx;
							height: 36rpx;
							border-radius: 6rpx;
							text-align: center;
							line-height: 36rpx;
							border: 1rpx solid #FF4040;
							color: #FF4040;
						}

						.ic1 {
							border-color: #FFA235;
							color: #FFA235;
						}
					}
				}

				.dong_one:nth-of-type(2) {
					padding-bottom: 0;
				}

				.button {
					width: 630rpx;
					height: 80rpx;
					background: #E9F5EE;
					border-radius: 12rpx;
					font-size: 30rpx;
					font-weight: 800;
					color: #38916C;
					line-height: 80rpx;
					text-align: center;
					margin-top: 40rpx;
				}

			}

		}

		//最新成交价
		.zui_price {
			margin: 0 30rpx;
			margin-bottom: 30rpx;
			height: auto;
			background: #fff;
			padding-left: 30rpx;
			padding-right: 30rpx;
			box-sizing: border-box;
			padding-bottom: 40rpx;
			border-radius: 16rpx;

			.tit {
				font-size: 34rpx;
				font-weight: 800;
				color: #19191A;
				line-height: 114rpx;

				.cha {
					font-size: 28rpx;
					font-weight: 500;
					color: #969699;
					float: right;
					line-height: 114rpx;

					text {
						font-size: 34rpx;
						color: #FF6040;
						font-weight: bold;
					}
				}
			}

			// .zhu_box {
			// 	.charts {
			// 		width: 750upx;
			// 		height: 500upx;
			// 		background-color: #FFFFFF;
			// 		margin-top: 40rpx;
			// 	}
			// }

			.table_box {
				width: 100%;
				height: auto;
				position: relative;
				margin-top: 10rpx;

				.table {
					width: 100%;
					height: auto;
					border-radius: 12rpx 12rpx 0 0;
					overflow: hidden;

					.tables {
						border-radius: 12rpx 12rpx 0 0;
					}

					.thead {
						background-color: #F2F2F2;
					}
				}

				.yincang {
					width: 691rpx;
					height: 100rpx;
					background: linear-gradient(0deg, #FFFFFF, rgba(255, 255, 255, 0));
					position: absolute;
					bottom: 0px;
					left: -30rpx;

					image {
						width: 36rpx;
						height: 36rpx;
						position: absolute;
						left: 50%;
						margin-left: -18rpx;
						bottom: 31rpx;
					}
				}
			}

			.bim {
				display: flex;
				align-items: center;
				margin-top: 14rpx;

				image {
					width: 36rpx;
					height: 36rpx;
					margin-right: 8rpx;
				}

				.swiper {
					height: 36rpx;
					flex: 1;

					.swiper-item {
						line-height: 36rpx;
						color: #646566;
						font-size: 27.88rpx;
						width: 478rpx;
						overflow: hidden;
						text-overflow: ellipsis;
						white-space: nowrap;
					}
				}
			}

			.get_di_price {
				width: 100%;
				height: 80rpx;
				background: #E9F5EE;
				font-size: 30rpx;
				font-weight: 800;
				color: #38916C;
				text-align: center;
				line-height: 80rpx;
				margin-top: 38rpx;
			}

		}

		//置业分析报告
		.fenxi {
			margin: 0 30rpx;
			border-radius: 16rpx;
			padding-left: 30rpx;
			padding-right: 30rpx;
			padding-bottom: 30rpx;
			background: #fff;
			box-sizing: border-box;
			position: relative;

			.tit {
				font-size: 34rpx;
				font-weight: 800;
				color: #1F1F1F;
				line-height: 114rpx;
			}

			.map {
				margin-bottom: 20rpx;

				image {
					width: 100%;
					height: 260rpx;
				}
			}

			.li {
				margin-bottom: 40rpx;

				.title {
					color: #121212;
					font-size: 30rpx;
					font-weight: bold;
					margin-bottom: 28rpx;

					image {
						width: 32rpx;
						height: 32rpx;
						margin-right: 10rpx;
					}
				}

				.licon {
					color: #323233;
					font-size: 28rpx;
					line-height: 42rpx;
					display: -webkit-box;
					-webkit-box-orient: vertical;
					-webkit-line-clamp: 2;
					overflow: hidden;
				}
			}

			.zhaobox {
				width: 750rpx;
				height: 116rpx;
				position: absolute;
				bottom: 110rpx;
				left: -30rpx;
				background: linear-gradient(0deg, #FFFFFF, rgba(255, 255, 255, 0));
				display: flex;
				justify-content: center;
				align-items: center;

				image {
					width: 32rpx;
					height: 32rpx;
				}
			}

			.btn {
				width: 100%;
				font-size: 30rpx;
				font-weight: 800;
				color: #38916C;
				height: 80rpx;
				background: #E9F5EE;
				border-radius: 12rpx;
				margin-top: 40rpx;
				text-align: center;
				line-height: 80rpx;
			}
		}

		//家园咨询师
		.ye_people {
			background-color: #fff;
			margin: 0 30rpx;
			border-radius: 16rpx;
			padding-left: 30rpx;
			padding-right: 30rpx;
			box-sizing: border-box;
			height: auto;
			padding-bottom: 10rpx;
			margin-bottom: 30rpx;

			.tit {
				font-size: 34rpx;
				font-weight: 800;
				color: #17181A;
				line-height: 114rpx;
			}

			.tese:after {
				display: block;
				content: '';
				height: 0;
				overflow: hidden;
				visibility: hidden;
				clear: both;
			}

			.tese {
				width: 100%;
				margin-bottom: 50rpx;

				view {
					font-size: 26rpx;
					font-weight: 500;
					color: #969699;
					float: left;
					display: flex;

					image {
						width: 32rpx;
						height: 32rpx;
					}
				}

				.te_01 {
					margin-right: 34rpx;
				}

				.te_02 {
					margin-right: 34rpx;
				}

				.te_03 {
					margin-right: 34rpx;
				}
			}

			.ye_one:after {
				display: block;
				content: '';
				height: 0;
				overflow: hidden;
				visibility: hidden;
				clear: both;
			}

			.ye_one {
				height: 80rpx;
				width: 100%;
				margin-bottom: 50rpx;
				display: flex;
				align-items: center;

				.head_img {
					width: 80rpx;
					height: 80rpx;
					border-radius: 40rpx;
					float: left;
					margin-right: 25rpx;
				}

				.peo {
					.top {
						font-size: 32rpx;
						font-weight: bold;
						color: #323233;
						display: flex;
						align-items: center;

						text {
							width: 115rpx;
							height: 30rpx;
							background: #FA941B;
							border-radius: 6rpx;
							font-size: 20rpx;
							font-weight: 500;
							color: #FFFFFF;
							display: inline-block;
							text-align: center;
							margin-left: 12rpx;
						}
					}

					.bottom {
						font-size: 24rpx;
						font-weight: 500;
						color: #7D7D80;
						margin-top: 10rpx;
					}
				}

				.bo_tel {
					margin-left: auto;
					display: flex;

					button {
						width: 80rpx;
						height: 80rpx;

						image {
							width: 80rpx;
							height: 80rpx;
							border-radius: 50%;
						}
					}

					image {
						width: 80rpx;
						height: 80rpx;
						border-radius: 50%;

					}

					.bo_zi {
						margin-right: 40rpx;
					}
				}
			}

		}

		// /周边配套/
		.zhou_pei {
			margin: 0 30rpx;
			background-color: #fff;
			height: auto;
			padding-bottom: 40rpx;
			margin-bottom: 30rpx;
			border-radius: 16rpx;

			.zhou {
				font-size: 34rpx;
				font-weight: 800;
				color: #1F1F1F;
				padding-left: 30rpx;
				padding-right: 30rpx;
				box-sizing: border-box;
				line-height: 114rpx;

			}

			.wei {
				padding-left: 30rpx;
				display: flex;
				margin-bottom: 25rpx;

				// align-items: center;
				.left {
					font-size: 30rpx;
					font-weight: 500;
					color: #969699;
					margin-right: 17rpx;
					margin-bottom: 8rpx;
					line-height: 32rpx;
				}

				.rig {
					width: 550rpx;
					overflow: hidden;
					text-overflow: ellipsis;
					white-space: nowrap;
					font-size: 30rpx;
					font-weight: 500;
					color: #323233;
					line-height: 32rpx;
					//margin-bottom: 32rpx;
				}



			}

			.pei {
				padding-left: 30rpx;
				padding-right: 30rpx;
				box-sizing: border-box;
				margin-bottom: 34rpx;
				display: flex;
				align-items: center;

				text {
					font-size: 30rpx;
					font-weight: 500;
					color: #969699;
					margin-right: 17rpx;
				}

				font-size: 30rpx;
				font-weight: 500;
				color: #628BB9;
				line-height: 36rpx;

				image {
					width: 36rpx;
					height: 36rpx;
				}
			}

			.scrollbox {
				white-space: nowrap;
				margin-bottom: 40rpx;

				.scrolls {
					view {
						display: inline-block;
						width: 132rpx;
						height: 56rpx;
						border-radius: 28rpx;
						text-align: center;
						line-height: 56rpx;
						background-color: #F5F5F5;
						color: #4B4B4D;
						font-size: 26rpx;
						margin-right: 20rpx;
					}

					.active {
						background-color: #E6FAEF;
						color: #2AC66D;
						font-weight: bold;
					}

					view:nth-of-type(1) {
						margin-left: 30rpx;
					}
				}
			}

			//周边配套地图
			.address {
				width: 100%;


				.map {
					width: 100%;
					height: 300rpx;
					padding-left: 30rpx;
					padding-right: 30rpx;
					box-sizing: border-box;
					position: relative;

					.nav_nav {
						width: 596rpx;
						height: 90rpx;
						background: #fff;
						position: absolute;
						bottom: 19rpx;
						z-index: 4000;
						display: flex;
						justify-content: space-between;
						padding-left: 27rpx;
						padding-right: 27rpx;
						margin-left: 20rpx;

						.nav_list {
							display: flex;
							align-items: center;

							image {
								width: 32rpx;
								height: 32rpx;
							}

							font-size: 26rpx;
							font-weight: 400;
							color: #3D3D3D;
						}

					}

					image {
						width: 630rpx;
						height: 300rpx;
					}
				}

				.add_all {
					padding-left: 30rpx;
					padding-right: 30rpx;
					box-sizing: border-box;

					.add_one {
						margin-bottom: 30rpx;

						.tit {
							font-size: 28rpx;
							font-weight: bold;
							color: #323233;
						}

						.bus {
							display: flex;
							margin-top: 15rpx;

							.left {
								font-size: 24rpx;
								font-weight: 500;
								color: #969799;
								line-height: 24rpx;
							}

							.rig {
								font-size: 24rpx;
								font-weight: 500;
								color: #969799;
								line-height: 24rpx;
							}
						}
					}
				}


			}

			.button {
				width: 630rpx;
				height: 80rpx;
				background: #E9F5EE;
				border-radius: 12rpx;
				font-size: 30rpx;
				font-weight: bold;
				color: #38916C;
				text-align: center;
				line-height: 80rpx;
				margin-left: 30rpx;
				margin-top: 50rpx;
			}
		}

		// 楼盘点评 
		.lou_dian {
			margin: 0 30rpx;
			height: auto;
			padding-left: 30rpx;
			padding-right: 30rpx;
			background: #ffff;
			border-radius: 16rpx;
			box-sizing: border-box;
			padding-bottom: 40rpx;
			margin-bottom: 30rpx;

			.top {
				height: 114rpx;
				width: 100%;
				margin-bottom: 10rpx;

				.tit {
					font-size: 34rpx;
					font-weight: 800;
					color: #121212;
					float: left;
					line-height: 114rpx;
				}

				.other {
					font-size: 26rpx;
					font-weight: 500;
					color: #969699;
					float: right;
					line-height: 114rpx;

					image {
						width: 24rpx;
						height: 24rpx;
					}
				}
			}

			.bottom {
				.ping_one:after {
					height: 0;
					content: '';
					display: block;
					overflow: hidden;
					visibility: hidden;
					clear: both;
				}

				.ping_one {
					width: 100%;
					margin-bottom: 50rpx;
					display: flex;

					.left {
						margin-right: 22rpx;

						image {
							width: 73rpx;
							height: 73rpx;
						}

					}

					.right {
						flex: 1;

						.top_tit {
							height: 32rpx;
							margin-bottom: 22rpx;

							.tel {
								font-size: 32rpx;
								font-weight: 800;
								color: #19191A;
								float: left;
								line-height: 32rpx;
							}

							.zan {
								font-size: 24rpx;
								font-weight: 400;
								color: #2FCB72;
								float: right;
								display: flex;
								align-items: center;

								image {
									width: 32rpx;
									height: 32rpx;
									margin-right: 6rpx;
								}
							}

							.no_zan {
								font-size: 24rpx;
								font-weight: 400;
								color: #B2B2B6;
								float: right;
								display: flex;
								align-items: center;
								margin-bottom: 20rpx;

								image {
									width: 32rpx;
									height: 32rpx;
									margin-right: 6rpx;
								}
							}

							button {
								line-height: 0rpx;

								.no_zan {
									font-size: 24rpx;
									font-weight: 400;
									color: #B2B2B6;
									float: right;
									display: flex;
									align-items: center;
									margin-bottom: 20rpx;

									image {
										width: 32rpx;
										height: 32rpx;
									}
								}
							}

						}

						.content {
							font-size: 28rpx;
							font-weight: 500;
							color: #333333;
							line-height: 44rpx;
							display: -webkit-box;
							-webkit-box-orient: vertical;
							-webkit-line-clamp: 2;
							overflow: hidden;
						}

						.time_box {
							margin-top: 5rpx;
							display: flex;

							.time {
								font-size: 24rpx;
								color: #AFAFB3;
								line-height: 26rpx;
							}

							.delete {
								font-size: 26rpx;
								font-weight: 500;
								color: #6888A2;
								margin-left: 19rpx;
								line-height: 26rpx;
								padding-left: 0rpx;
								padding-right: 0rpx;
								margin-top: 10rpx;
							}
						}

					}
				}

				.zanwu {
					font-size: 26rpx;
					color: #000;
					height: 100rpx;
					line-height: 100rpx;
					text-align: center;
					margin-bottom: 20rpx;
				}
			}

			.dian_btn {
				width: 100%;
				height: 80rpx;
				background-color: #E9F5EE;

				font-size: 30rpx;
				font-weight: 800;
				color: #38916C;

				line-height: 80rpx;
				text-align: center;
			}
		}

		//楼盘问答
		.lou_wenda {
			background-color: #fff;
			margin: 0 30rpx;
			height: auto;
			padding-left: 30rpx;
			padding-right: 30rpx;
			box-sizing: border-box;
			padding-bottom: 40rpx;
			border-radius: 16rpx;
			margin-bottom: 30rpx;

			.title {
				height: 114rpx;
				width: 100%;

				.title_l {
					font-size: 34rpx;
					font-weight: 800;
					color: #121212;
					line-height: 114rpx;
					float: left;
				}

				.all_wen {
					font-size: 26rpx;
					font-weight: 500;
					color: #969699;
					line-height: 114rpx;
					float: right;

					image {
						width: 24rpx;
						height: 24rpx;
					}
				}
			}

			.wen_list {
				.wen_one {
					margin-bottom: 15rpx;

					.wen_top {
						.wen {
							width: 30rpx;
							height: 30rpx;
							background: #FF5454;
							border-radius: 4rpx;
							font-size: 20rpx;
							font-weight: 400;
							color: #FFFFFF;
							text-align: center;
							line-height: 30rpx;
							display: inline-block;
							margin-right: 20rpx;
						}

						.wen_t {
							font-size: 28rpx;
							font-weight: 500;
							color: #323233;
							line-height: 44rpx;
						}
					}

					.da {
						font-size: 26rpx;
						font-weight: 500;
						color: #969699;
						line-height: 44rpx;
					}
				}

				.zanwu {
					font-size: 26rpx;
					color: #000;
					height: 100rpx;
					line-height: 100rpx;
					text-align: center;
					margin-bottom: 20rpx;
				}

				.ti_btn {
					width: 100%;
					height: 80rpx;
					line-height: 80rpx;
					font-size: 30rpx;
					font-weight: bold;
					color: #38916C;
					line-height: 80rpx;
					text-align: center;
					background: #E9F5EE;
					margin-top: 35rpx;
				}
			}
		}

		//看了该楼盘的人还看了
		.about_lou {
			background-color: #fff;
			padding-left: 30rpx;
			padding-right: 30rpx;
			margin: 0 30rpx;
			box-sizing: border-box;
			padding-bottom: 75rpx;
			border-radius: 16rpx;
			margin-bottom: 30rpx;

			.tit {
				font-size: 34rpx;
				font-weight: 800;
				color: #17181A;
				line-height: 114rpx;
			}

			.pro_list {
				.peo_one:after {
					content: '';
					height: 0;
					clear: both;
					overflow: hidden;
					visibility: hidden;
					display: block;
				}

				.peo_one {
					margin-bottom: 60rpx;

					image {
						width: 220rpx;
						height: 160rpx;
						border-radius: 12rpx;
						float: left;
						margin-right: 30rpx;
					}

					.right_pro {
						width: 440rpx;
						float: right;

						.pro_name {
							display: flex;
							justify-content: space-between;

							.name {
								font-size: 32rpx;
								font-weight: 800;
								color: #303233;
								line-height: 32rpx;
								width: 352rpx;
								overflow: hidden;
								white-space: nowrap;
								text-overflow: ellipsis;
								display: block;
							}


							.status {
								width: 68rpx;
								height: 36rpx;
								background: #E6FAF0;
								border-radius: 6rpx;
								font-size: 22rpx;
								font-weight: 500;
								color: #20C657;
								line-height: 36rpx;
								float: right;
								text-align: center;
							}
						}

						.price {
							font-size: 32rpx;
							font-weight: 800;
							color: #FF6040;
							line-height: 56rpx;
						}

						.type {
							font-size: 24rpx;
							font-weight: 500;
							color: #646466;

							text {
								padding-left: 16rpx;
								padding-right: 16rpx;
							}
						}

						.tese {
							.zhuang {
								width: 68rpx;
								height: 36rpx;
								background: #EBF8FF;
								border-radius: 6rpx;
								font-size: 22rpx;
								font-weight: 500;
								color: #3EACF0;
								line-height: 36rpx;
								margin-right: 12rpx;
								display: inline-block;
								text-align: center;
							}

							.other {
								font-size: 22rpx;
								font-weight: 500;
								color: #7D7D80;
								padding: 5rpx 14rpx;
								background-color: #F5F5F5;
								margin-right: 12rpx;
							}
						}
					}
				}
			}
		}

		// 相关资讯
		.lou_infos {
			background-color: #fff;
			padding-left: 30rpx;
			padding-right: 30rpx;
			margin: 0 30rpx;
			border-radius: 16rpx;
			margin-bottom: 30rpx;

			.infos-tit {
				padding-top: 40rpx;
				margin-bottom: 48rpx;

				.kk {
					color: #121212;
					font-size: 32rpx;
					font-weight: bold;
				}

				.right {
					color: #646466;
					font-size: 28rpx;
					float: right;

					image {
						width: 24rpx;
						height: 24rpx;
					}
				}
			}

			.ul {
				.li {
					color: #323233;
					font-size: 28rpx;
					line-height: 44rpx;
					padding-bottom: 30rpx;
					border-bottom: 1rpx solid #F2F2F2;
					margin-bottom: 26rpx;
				}

				.li:before {
					content: "";
					display: inline-block;
					width: 6rpx;
					height: 6rpx;
					background-color: #333;
					border-radius: 50%;
					margin-right: 5rpx;
					vertical-align: middle;
				}

				.li:last-child {
					border: 0;
					margin-bottom: 14rpx;
				}
			}
		}

		.rules {
			width: 650rpx;
			height: 750rpx;
			background: #FFFFFF;
			border-radius: 24rpx;
			position: absolute;
			top: 50%;
			left: 50%;
			transform: translate(-50%, -50%);
			padding-left: 30rpx;
			padding-right: 30rpx;
			box-sizing: border-box;

			.title {
				font-size: 34rpx;
				font-weight: 800;
				color: #19191A;
				line-height: 114rpx;
			}

			.text_box {
				width: 100%;
				height: 580rpx;

				view {
					font-size: 26rpx;
					font-weight: 500;
					color: #999999;
					line-height: 44rpx;
				}
			}
		}

		// im弹框
		.talkbox {
			padding: 50rpx 50rpx 0 50rpx;

			.topbox {
				display: flex;
				align-items: center;
				margin-bottom: 40rpx;

				.imgbos {
					width: 96rpx;
					height: 96rpx;
					border-radius: 50%;
					overflow: hidden;
					margin-right: 24rpx;

					image {
						width: 96rpx;
						height: 96rpx;
					}
				}

				.peoname {
					.name {
						color: #1F1F1F;
						font-size: 34rpx;
						font-weight: bold;
						margin-bottom: 18rpx;

						text {
							color: #7495BD;
							font-size: 22rpx;
							padding: 6rpx 10rpx;
							background-color: #F2F8FF;
							border-radius: 3rpx;
						}
					}

					.peonum {
						color: #646466;
						font-size: 26rpx;
					}
				}
			}

			.peomsg {
				display: flex;
				margin-bottom: 60rpx;

				.li {
					text-align: center;
					width: 33%;
					border-right: 1rpx solid #F0F0F2;

					.litop {
						color: #121212;
						font-size: 36rpx;
						font-weight: bold;

						text {
							color: #121212;
							font-size: 20rpx;
						}
					}

					.libom {
						margin-top: 12rpx;
						color: #646466;
						font-size: 22rpx;
					}
				}

				.li:nth-of-type(3) {
					border: 0;
				}
			}

			.btn {
				display: flex;

				button {
					width: 300rpx;
					height: 80rpx;
					border-radius: 8rpx;
					text-align: center;
					line-height: 80rpx;
					border: 0;
					outline: none;
					font-size: 30rpx;
				}

				.talknow {
					color: #FFFFFF;
					background: linear-gradient(-270deg, #348aff, #6accff);
				}

				.talklate {
					background-color: #edf5fa;
					border: 1rpx solid #3eacf0;
					color: #3eacf0;
					margin-left: auto;
				}
			}
		}

	}
</style>
