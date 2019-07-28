<template>
	<view class="loadmore">
		<slot name="list"  :items="items" ></slot>
		<view class="uni-load-more">
			
			<view v-show="state === 1" class="uni-load-more__img">
				<view class="load1 load">
					<view :style="{ background: color }" class="uni-load-view_wrapper" />
					<view :style="{ background: color }" class="uni-load-view_wrapper" />
					<view :style="{ background: color }" class="uni-load-view_wrapper" />
					<view :style="{ background: color }" class="uni-load-view_wrapper" />
				</view>
				<view class="load2 load">
					<view :style="{ background: color }" class="uni-load-view_wrapper" />
					<view :style="{ background: color }" class="uni-load-view_wrapper" />
					<view :style="{ background: color }" class="uni-load-view_wrapper" />
					<view :style="{ background: color }" class="uni-load-view_wrapper" />
				</view>
				<view class="load3 load">
					<view :style="{ background: color }" class="uni-load-view_wrapper" />
					<view :style="{ background: color }" class="uni-load-view_wrapper" />
					<view :style="{ background: color }" class="uni-load-view_wrapper" />
					<view :style="{ background: color }" class="uni-load-view_wrapper" />
				</view>
			</view>
			<text :style="{ color: color }" class="uni-load-more__text" v-show="state ==2 && items.length == 0">
				暂无数据...
			</text>
			<text :style="{ color: color }" class="uni-load-more__text" v-show="!(state ==2 && items.length == 0)">
				{{StateText}}
			</text>
		</view>
	</view>
</template>

<script>
	export default {
		props:{
			pageSize:{
				type:Number, 
				default:10
			},
			params:{
				type:Object,
				default:function(){
					return {}
				}
			},
			color: {
				type: String,
				default: '#777777'
			},
			reachBottomCount:{
				type:Number, 
				default:0
			},
			pullDownRefreshCount:{
				type:Number,
				default:0
			},
			getData:{
				type:Function,
				default:function(){
					return ()=>{ return new Promise((resolve, reject) => {
						setTimeout(function () {
							
							console.log('default getData');
							resolve([]);
						}, 3000);
					});
					}
				}
			}
		},
		watch:{
			reachBottomCount(nv,ov){
				this._initData()
			},
			pullDownRefreshCount(nv,ov){
				this.reLoadData()
				uni.stopPullDownRefresh()
			}
		},
		computed:{
			StateText(){
				var text = '';
				switch(this.state){
					case 0:text = '上拉加载更多...';break;
					case 1:text = '加载中，请稍后...';break;
					case 2:text = '无更多内容...';break;
					case 3:text = '发生错误，请重试...';break;
					default:text = '无更多内容...';break;
				}
				return text;
			}
		},
		data() {
			return {
				state:0,
				items:[],
				pageNo:1,
				
			}
		},
		methods: {
			async _initData(){
				if(this.state>0)
				{
					return ;
				}
				this.state = 1;
				var r =null;
				try{
					r = await this.getData();
					this.state = 0;
				}catch(e){
					//TODO handle the exception
					r = [];
					this.state = 3;
					return;
				}
				
				if(r.length < this.pageSize)
					this.state = 2;
				else 
					this.pageNo ++;
				this.items = this.items.concat(r);
			},
			reLoadData(){
				this.items = [],
				this.pageNo = 1,
				this.state = 0;
				this._initData();
			}
		}
	}
</script>

<style>
	
	
	.loadmore{
		display: flex;
		flex-direction: column;
	}
	
	@charset "UTF-8";
	
	.uni-load-more {
		display: flex;
		flex-direction: row;
		height: 80upx;
		align-items: center;
		justify-content: center
	}
	
	.uni-load-more__text {
		font-size: 28upx;
		color: #999
	}
	
		.uni-load-more__img {
		height: 24px;
		width: 24px;
		margin-right: 10px
	}
	
	.uni-load-more__img>.load {
		position: absolute
	}
	
	.uni-load-more__img>.load .uni-load-view_wrapper {
		width: 6px;
		height: 2px;
		border-top-left-radius: 1px;
		border-bottom-left-radius: 1px;
		background: #999;
		position: absolute;
		opacity: .2;
		transform-origin: 50%;
		animation: load 1.56s ease infinite
	}
	
	.uni-load-more__img>.load .uni-load-view_wrapper:nth-child(1) {
		transform: rotate(90deg);
		top: 2px;
		left: 9px
	}
	
	.uni-load-more__img>.load .uni-load-view_wrapper:nth-child(2) {
		transform: rotate(180deg);
		top: 11px;
		right: 0
	}
	
	.uni-load-more__img>.load .uni-load-view_wrapper:nth-child(3) {
		transform: rotate(270deg);
		bottom: 2px;
		left: 9px
	}
	
	.uni-load-more__img>.load .uni-load-view_wrapper:nth-child(4) {
		top: 11px;
		left: 0
	}
	
	.load1,
	.load2,
	.load3 {
		height: 24px;
		width: 24px
	}
	
	.load2 {
		transform: rotate(30deg)
	}
	
	.load3 {
		transform: rotate(60deg)
	}
	
	.load1 .uni-load-view_wrapper:nth-child(1) {
		animation-delay: 0s
	}
	
	.load2 .uni-load-view_wrapper:nth-child(1) {
		animation-delay: .13s
	}
	
	.load3 .uni-load-view_wrapper:nth-child(1) {
		animation-delay: .26s
	}
	
	.load1 .uni-load-view_wrapper:nth-child(2) {
		animation-delay: .39s
	}
	
	.load2 .uni-load-view_wrapper:nth-child(2) {
		animation-delay: .52s
	}
	
	.load3 .uni-load-view_wrapper:nth-child(2) {
		animation-delay: .65s
	}
	
	.load1 .uni-load-view_wrapper:nth-child(3) {
		animation-delay: .78s
	}
	
	.load2 .uni-load-view_wrapper:nth-child(3) {
		animation-delay: .91s
	}
	
	.load3 .uni-load-view_wrapper:nth-child(3) {
		animation-delay: 1.04s
	}
	
	.load1 .uni-load-view_wrapper:nth-child(4) {
		animation-delay: 1.17s
	}
	
	.load2 .uni-load-view_wrapper:nth-child(4) {
		animation-delay: 1.3s
	}
	
	.load3 .uni-load-view_wrapper:nth-child(4) {
		animation-delay: 1.43s
	}
	
	@-webkit-keyframes load {
		0% {
			opacity: 1
		}
	
		100% {
			opacity: .2
		}
	}
</style>
