<template>
	<view>
		<web-view :src="url" v-if="url.length"></web-view>
	</view>
</template>

<script>
	export default {
		onLoad(options) {
			let name = uni.getStorageSync('cityname')
			let id = options.id
			let city = uni.getStorageSync('city')
			let pro = options.bid || 0
			let uuid = uni.getStorageSync('uuid')
			let staffid = options.id
			let url = `https://m.jy1980.com/chengdu/jytalk/${uuid}/${staffid}/${pro},${city}?uuid=${uuid}`
			this.url = url
			console.log(url)
			//#ifdef MP-BAIDU
			swan.setPageInfo({
				title: '家园新房-im聊天',
				keywords: '家园新房-im聊天',
				description: '家园新房-im聊天',
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
