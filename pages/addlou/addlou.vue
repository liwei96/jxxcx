<template>
	<view class="addlou">
		<!-- <view class="toptitle">
			 <view class="status_bar">
			  </view>
			<navigator open-type="navigateBack" delta="1">
				<image src="../../static/all-back.png" mode=""></image>
				<text>楼盘对比</text>
			</navigator>
		</view> -->
		<checkbox-group @change="getChecked" >
		<view class="pro_list">
			<view class="sel_pro" v-for="item in data" :key="item.id">
					<label class="left_checkbox">
						<checkbox :value="item.id" :checked="checked" color="#20C657"/>
					</label>
				
					<view class="pro_one">
						<image :src="item.img" mode=""></image>
						<view class="right_name">
							<view class="name">{{item.name}}</view>
							<view class="price">{{item.price}}元/m²</view>
							<view class="type">{{item.type}}<text class="ge">|</text>{{item.city}}-{{item.country}}<text class="ge">|</text>{{item.area}}m²</view>
							<view class="tese">
								<text class="zhuang" v-if="item.decorate!=='' && item.decorate!==null">{{item.decorate}}</text>
								<template v-if="item.features">
								   <text class="other" v-for="(ite,index) in item.features" :key="index">{{ite}}</text>
								</template>
							</view>
						</view>
					</view>
			</view>
			
		</view>
		<view class="bg_hui"></view>
		<!-- 浏览足迹 -->
		<view class="liu_zu" v-if="foots.length>0">
			<view class="tit">浏览足迹</view>
			<view class="sel_pro" v-for="item in foots" :key="item.id">
					<label class="left_checkbox">
						<checkbox :value="item.id" :checked="checked" />
					</label>
				<view class="pro_one" >
					<image :src="item.img" mode=""></image>
					<view class="right_name">
						<view class="name">{{item.name}}</view>
						<view class="price">{{item.price}}元/m²</view>
						<view class="type">{{item.type}}<text class="ge">|</text>{{item.city}}-{{item.country}}<text class="ge">|</text>{{item.area}}m²</view>
						<view class="tese">
							<text class="zhuang" v-if="item.decorate!==null && item.decorate!=='null'">{{item.decorate}}</text>
							<text class="other" v-if="item.type!=='' && item.type!==null && item.type!=='null'">{{item.type}}</text>
							<text class="other"  v-for="ite in item.features" >
								<text v-if="ite!==null && ite!=='' && ite!=='null'&& ite!==undefined">
						     	  {{ite}}
							    </text>
							</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		</checkbox-group>
	    <!-- 添加楼盘按钮 -->
	     <view class="add_btn_box">
	     	<view :class="{active:active}"  @tap="addLou">
	     		添加楼盘
	     	</view>
	     </view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				checked:false,
				active:false,
				common:{},
				data:[],
				checkValue:[],
				
				project_id:[],
				foots:[]
			};
		},
		onLoad(option){
			this.getSome();
			console.log(option);
			this.project_id = option.id;
		},
		methods:{
			addLou(){
				let ids = this.checkValue;
				console.log(this.project_id);
				if(this.project_id!==""  && this.project_id!=="cb"){
					this.checkValue.push(this.project_id);
				}else{
					this.checkValue = this.checkValue;
				}
				
				let newsel = ids.join(',');
			    uni.navigateTo({
			    	url:"../loupk/loupk?ids="+newsel
			    })
			},
			getChecked(val){
				this.checkValue = val.detail.value;
				
				if(val.detail.value.length!==0){
					 this.active=true;
				}
			},
			getSome(){
				let city_id  = uni.getStorageSync("city");
				let token  = uni.getStorageSync("token");
				uni.request({
					url:this.apiserve+'/applets/compare/recommend',
					method:"GET",
					data:{
						city:city_id,
						token:token,
						other: uni.getStorageSync('other'),
						uuid: uni.getStorageSync('uuid')
					},
					success:(res)=> {
					   if(res.data.code==200){
						   console.log(res);
						 //  this.common = res.data.common;
						   this.data = res.data.recommends;
						   let arr = res.data.foots;
						   arr.map(p=>{
							  p.features =  p.features.filter(n => n)
						   })
						  this.foots = arr;
					   }
					}
				})
			}
		}
	}
</script>

<style lang="less">
	page{
		background:#fff;
	}
.addlou{
	padding-left: 30rpx;
	padding-right:30rpx;
	box-sizing: border-box;
	width: 100%;
	.toptitle {
		color: #D4D7D9;
		font-size: 29.88rpx;
		// padding: 0 29.88rpx;
		line-height: 87.64rpx;
		background-color: #fff;
		position: fixed;
		top: 0;
		width: 100%;
		z-index: 30000;
		.status_bar {
		      height: var(--status-bar-height);
		      width: 100%;
		  }
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
			color: #17191A;
		}
	}
	.pro_list {
			width: 100%;
			position: relative;
			//列表部分
		.sel_pro:after {
			height: 0;
			overflow: hidden;
			visibility: hidden;
			clear: both;
			content: '';
			display: block;
		}
		.sel_pro {
			width: 120%;
			height: auto;
			margin-bottom: 60rpx;
			display:flex;
			.left_checkbox {
				float: left;
				margin-top: 50rpx;
				margin-right: 13rpx;
			}

			.pro_one {
				float: left;
				display:flex;
				image {
					width: 220rpx;
					height: 160rpx;
					border-radius: 12rpx;
					float: left;
					margin-right: 30rpx;
				}

				.right_name {
					width: 610rpx;
					height: 160rpx;
					
					.name {
						font-size: 30rpx;
						font-weight: 800;
						color: #303233;
						line-height: 30rpx;
					}

					.price {
						font-size: 32rpx;
						font-weight: bold;
						color: #FF6040;
						line-height: 54rpx;
					}

					.type {
						font-size: 24rpx;
						font-weight: 500;
						color: #646566;
						line-height: 24rpx;
						margin-bottom: 12rpx;

						.ge {
							padding-left: 14rpx;
							padding-right: 14rpx;
						}
					}

					.tese {
						.zhuang {
							width: 68rpx;
							height: 36rpx;
							background: #F0F5F9;
							border-radius: 4rpx;
							font-size: 24rpx;
							font-weight: 500;
							color: #40A2F4;
							line-height: 36rpx;
							text-align: center;
							display: inline-block;
							margin-right: 12rpx;
						}

						.other {
							font-size: 24rpx;
							font-weight: 500;
							color: #7D7E80;
							padding: 4rpx 12rpx;
							background: #F5F5F5;
							margin-right: 12rpx;
							border-radius: 4rpx;
						}
					}
				}
			}

		}
			.sel_pro:first-child{
				margin-top: 40rpx;
			}
	
		}	
		.bg_hui{
			width:100%;
			height: 20rpx;
			background: #F7F7F7;
		}
	 //浏览足迹
	.liu_zu{
		margin-bottom: 150rpx;
		.tit{
			font-size: 32rpx;
			font-weight: 800;
			color: #303233;
			line-height: 112rpx;
		}
		.sel_pro:after {
			height: 0;
			overflow: hidden;
			visibility: hidden;
			clear: both;
			content: '';
			display: block;
		}
		.sel_pro {
			width: 120%;
			height: auto;
			margin-bottom: 60rpx;
			display: flex;
			.left_checkbox {
				float: left;
				margin-top: 50rpx;
				margin-right: 13rpx;
			}
		
			.pro_one {
				float: left;
				display: flex;
				image {
					width: 220rpx;
					height: 160rpx;
					border-radius: 12rpx;
					float: left;
					margin-right: 30rpx;
				}
		
				.right_name {
					width: 610rpx;
					height: 160rpx;
		
					.name {
						font-size: 30rpx;
						font-weight: 800;
						color: #303233;
						line-height: 30rpx;
					}
		
					.price {
						font-size: 32rpx;
						font-weight: bold;
						color: #FF6040;
						line-height: 54rpx;
					}
		
					.type {
						font-size: 24rpx;
						font-weight: 500;
						color: #646566;
						line-height: 24rpx;
						margin-bottom: 12rpx;
		
						.ge {
							padding-left: 14rpx;
							padding-right: 14rpx;
						}
					}
		
					.tese {
						.zhuang {
							width: 68rpx;
							height: 36rpx;
							background: #F0F5F9;
							border-radius: 4rpx;
							font-size: 24rpx;
							font-weight: 500;
							color: #40A2F4;
							line-height: 36rpx;
							text-align: center;
							display: inline-block;
							margin-right: 12rpx;
						}
		
						.other {
							font-size: 24rpx;
							font-weight: 500;
							color: #7D7E80;
							padding: 4rpx 12rpx;
							background: #F5F5F5;
							margin-right: 12rpx;
							border-radius: 4rpx;
						}
					}
				}
			}
		
		}
	}
	//添加楼盘按钮
	.add_btn_box{
		width:100%;
		background: #fff;
		height: 120rpx;
		padding: 20rpx 30rpx;
		box-sizing: border-box;
		position: fixed;
		bottom: 0rpx;
		left: 0rpx;
		view{
			width:100%;
			height: 80rpx;
			background: #76DC98;
			opacity: 0.5;
			border-radius: 12rpx;
			font-size: 30rpx;
			font-weight: bold;
			color: #FFFFFF;
			text-align: center;
			line-height: 80rpx;
		}
		.active{
			background:#20C657;
			opacity: 1;
		}

	}
	
}
</style>
