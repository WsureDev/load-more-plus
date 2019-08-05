<template>
	<view style="flex: 1;height: 100%;">
		<ws-slm ref="slm" @providers="providers" style="height: 100%;" class="swiper-box">
			<template v-slot:list_guanzhu="{ items }" class="swiper-box"> <!-- 此处为插槽，只能使用template或其他自定义component -->
			<!-- <scroll-view class="list" scroll-y @scrolltolower="loadMore"> -->
				<view class="solid-top" v-for="(item,index) in items" :key="index">
					<view class="item"> {{item.name}} </view>
				</view>
			<!-- </scroll-view> -->
			</template>
			<template v-slot:list_tuijian="{ items }"> <!-- 此处为插槽，只能使用template或其他自定义component -->
				<view class="solid-top" v-for="(item,index) in items" :key="index">
					<view class="item"> {{item.name}} </view>
				</view>
			</template>
			<template v-slot:list_tiyu="{ items }"> <!-- 此处为插槽，只能使用template或其他自定义component -->
				<view class="solid-top" v-for="(item,index) in items" :key="index">
					<view class="item"> {{item.name}} </view>
				</view>
			</template>
			<template v-slot:list_redian="{ items }"> <!-- 此处为插槽，只能使用template或其他自定义component -->
				<view class="solid-top" v-for="(item,index) in items" :key="index">
					<view class="item"> {{item.name}} </view>
				</view>
			</template>
		</ws-slm>
	</view>
</template>

<script>
	import wsSlm from '../../components/wsure-swiper-load-more/swiper-load-more.vue'
	export default {
		components:{
			wsSlm
		},
		data() {
			return {
				
			}
		},
		methods: {
			providers(e){
				console.log(e);
				var that = this;
				//模拟网络请求的延迟
				setTimeout(function () {
					
					var res = that.getData(e);
					
					console.log("pushData!")
					that.$refs.slm.pushData(res,e.index)
				}, 300);
				
			},
			getData(e){
				//模拟接口返回的第pageNo页的页数据
				var res = [];
				for(var i=0;i<e.pageSize;i++){
					var s = {
						name:e.tabId+'_'+e.pageNo+'_'+i,
					}
					res.push(s);
				}
				return res;
			},
			loadMore(){
				this.$refs.slm.loadMore();
			}
		},
		onReady() {
			this.$refs.slm.init()
		},
		onReachBottom() {
			this.$refs.slm.loadMore();
		},
		onPullDownRefresh() {
			this.$refs.slm.reLoadData()
		}
	}
</script>

<style>

.item{
	 display: flex;
	 justify-content: center;
	 align-content: center;
	 font-size: 60upx;
	 line-height: 150upx;
	 margin: 10upx 20upx;
	 border-radius: 20upx;
	 width: 710upx;
	 height: 150upx;
	 background: #CCCCCC;
 }
</style>
