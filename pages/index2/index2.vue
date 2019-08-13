<template>
	<view style="flex: 1;height: 100%;">
		<ws-slm ref="slm" @providers="providers" style="height: 100%;" class="swiper-box" :tabBars="tabBar">
			<!-- 统一处理，直接循环 -->
			<block v-for="(slotItem,slotIndex) in tabBar" :key="slotIndex">
				<template v-slot:[slotItem.id]="{ items }" class="swiper-box"> <!-- 此处为插槽，只能使用template或其他自定义component -->
					<view class="solid-top" v-for="(item,index) in items" :key="index">
						<view class="item"> {{item.name}} </view>
					</view>
				</template>
			</block>
			
			
			<!-- 个性处理，单独使用slot名称区别对待 -->
			<!-- <template v-slot:tuijian="{ items }"> 
				<view class="solid-top" v-for="(item,index) in items" :key="index">
					<view class="item"> {{item.name}} ———A</view>
				</view>
			</template>
			<template v-slot:tiyu="{ items }"> 
				<view class="solid-top" v-for="(item,index) in items" :key="index">
					<view class="item"> {{item.name}} ———B</view>
				</view>
			</template>
			<template v-slot:redian="{ items }"> 
				<view class="solid-top" v-for="(item,index) in items" :key="index">
					<view class="item"> {{item.name}} ———C</view>
				</view>
			</template> -->
		</ws-slm>
	</view>
</template>

<script>
	import wsSlm from '@/components/wsure-swiper-load-more/tabbar.nvue'
	export default {
		components:{
			wsSlm
		},
		data() {
			return {
				tabBar:[{
						name: '关注',
						id: 'guanzhu'
					}, {
						name: '推荐',
						id: 'tuijian'
					}, {
						name: '体育',
						id: 'tiyu'
					}, {
						name: '热点',
						id: 'redian'
					}]
			}
		},
		methods: {
			providers(e){
				//e包含4个参数:pageNo、pageSize、index、tabId
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
