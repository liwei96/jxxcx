<template>
	<view class="content">
		<web-view :src="url" v-if="url.length"></web-view>
	</view>
</template>

<script>
	export default {
		onLoad(options) {
			let name = uni.getStorageSync('cityname')
			let id = options.id
			let url = ''
			let tel = uni.getStorageSync('phone')
			if(tel) {
				url = `https://m.jy1980.com/hangzhou/rim/${id}/1/name?uuid=kWNznYTEzB3s1608096182000&isbaidu=1&phone=${tel}`
			}else {
				url = `https://m.jy1980.com/hangzhou/rim/${id}/1/name?uuid=kWNznYTEzB3s1608096182000&isbaidu=1`
			}
			// url = url+'&cityname='+name
			this.url = url
			console.log(url)
			//#ifdef MP-BAIDU
			swan.setPageInfo({
				title: '家园新房-周边详情',
				keywords: '家园新房-周边详情',
				description: '家园新房-周边详情',
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
				url: ''
			}
		},
		methods: {

		}
	}
</script>

<style>

</style>
