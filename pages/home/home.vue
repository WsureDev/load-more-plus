<template>
	<view>
		<view v-for="(item,index) in pageList" :key="index" class="item" @tap="go(item)">
			{{item.name}}
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				pageList:[
					{
						url:'/pages/index/index',
						name:'分页插件的基础使用demo'
					},
					{
						url:'/pages/index2/index2',
						name:'siper+nav的动态插槽的demo(非小程序平台)'
					},
					{
						url:'/pages/index/index',
						name:'siper+nav的动态插槽的demo(小程序平台)'
					}
				]
			}
		},
		methods: {
			go(e){
				uni.navigateTo({
					url:e.url
				})
			}
		}
	}
</script>

<style>
 .item{
	 display: flex;
	 justify-content: center;
	 align-content: center;
	 font-size: 32upx;
	 line-height: 150upx;
	 margin: 10upx 20upx;
	 border-radius: 20upx;
	 width: 710upx;
	 height: 150upx;
	 background: #CCCCCC;
 }
</style>
