<template>
	<view class="uni-tab-bar">
		<scroll-view id="tab-bar" class="uni-swiper-tab" scroll-x :scroll-left="scrollLeft">
			<view v-for="(tab,index) in tabBars" :key="tab.id" class="swiper-tab-list" :class="tabIndex==index ? 'active' : ''"
			 :id="tab.id" :data-current="index" @click="tapTab">{{tab.name}}</view>
		</scroll-view>
		<swiper :current="tabIndex" class="swiper-box " :duration="300" @change="changeTab">
			<swiper-item v-for="(tab,index1) in tabBars" :key="index1">
				<!-- <scroll-view class="list" scroll-y @scrolltolower="loadMore(index1)"> -->
					
					<ws-load-more class="list" :ref="'loadMore_'+index1" :pageSize="pageSize" @provider="provider">
						<template v-slot:list="{ items }"> <!-- 此处为插槽，只能使用template或其他自定义component -->
							
							<slot :name="'list_'+tabBars[index1].id" :items="items"></slot>
							
						</template>
					</ws-load-more>
				<!-- </scroll-view> -->
			</swiper-item>
		</swiper>
	</view>
</template>

<script>
	import wsLoadMore from '../wsure-load-more/load-more.vue';
	export default {
		props:{
			pageSize:{
				type:Number,
				default:10
			},
			tabBars:{
				type:Array,
				default:function(){
					return []
				}
			}
		},
		components:{
			wsLoadMore
		},
		data() {
			return {
				scrollLeft: 0,
				isClickChange: false,
				tabIndex: 0,
			};
		},
		methods:{
			provider(e){
				e.index = this.tabIndex;
				e.tabId = this.tabBars[this.tabIndex].id;
				this.$emit('providers',e)
			},
			pushData(arr,index){
				this.$refs['loadMore_'+index][0].pushData(arr);
			},
			loadMore(){
				this.$refs['loadMore_'+this.tabIndex][0].reachBottom();
			},
			reLoadData(){
				console.log(this.$refs['loadMore_'+this.tabIndex])
				this.$refs['loadMore_'+this.tabIndex][0].pullDownRefresh()
			},
			init(){
				this.tabIndex = 0;
				this.reLoadData()
			},
			async changeTab(e) {
				let index = e.target.current;
                this.tabIndex = index;
				/**
				 * 初始化下个Tab的数据
				 */
				if(this.$refs['loadMore_'+index][0].dataList.length ==0 &&this.$refs['loadMore_'+index][0].lastPage.length ==0)
				{
					this.$refs['loadMore_'+index][0].reLoadData()
				}
				
				if (this.isClickChange) {
					this.isClickChange = false;
					return;
				}
				let tabBar = await this.getElSize("tab-bar"),
					tabBarScrollLeft = tabBar.scrollLeft;
				let width = 0;

				for (let i = 0; i < index; i++) {
					let result = await this.getElSize(this.tabBars[i].id);
					width += result.width;
				}
				let winWidth = uni.getSystemInfoSync().windowWidth,
					nowElement = await this.getElSize(this.tabBars[index].id),
					nowWidth = nowElement.width;
				if (width + nowWidth - tabBarScrollLeft > winWidth) {
					this.scrollLeft = width + nowWidth - winWidth;
				}
				if (width < tabBarScrollLeft) {
					this.scrollLeft = width;
				}
				this.isClickChange = false;
			},
			getElSize(id) { //得到元素的size
				return new Promise((res, rej) => {
					uni.createSelectorQuery().select("#" + id).fields({
						size: true,
						scrollOffset: true
					}, (data) => {
						res(data);
					}).exec();
				})
			},
			async tapTab(e) { //点击tab-bar
				let tabIndex = e.target.dataset.current;
				
				if (this.tabIndex === tabIndex) {
					return false;
				} else {
					let tabBar = await this.getElSize("tab-bar"),
						tabBarScrollLeft = tabBar.scrollLeft; //点击的时候记录并设置scrollLeft
					this.scrollLeft = tabBarScrollLeft;
					this.isClickChange = true;
					this.tabIndex = tabIndex;
				}
			}
		},
		mounted() {
			this.reLoadData()
		}
	}
</script>

<style>
	.swiper-height{
		height: 1200upx;
	}
</style>
