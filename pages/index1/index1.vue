<template>
	<view>
		<ws-lm :getData="randomData" :params="params" :pullDownRefreshCount="reloadCount" :reachBottomCount="loadCount" :pageSize="pageSize">
			<template v-slot:list="{ items }"> <!-- 此处为插槽，只能使用template或其他自定义component -->
				<view class="solid-top" v-for="(item,index) in items" :key="index">
					<view class="item"> {{item.name}} </view>
				</view>
			</template>
		</ws-lm>
	</view>
</template>

<script>
	/* 
		这是一个半吊子的插件和它的使用demo
		前提：由于当时不知道有$ref导致闹出了笑话
		思路：在插件内维护pageNo和pageSize，将请求数据的逻辑封装成Promise用function包裹起来传入插件，由插件内部执行
			上拉下拉事件监听使用变量+watch（当时不知道有$ref闹出的笑话）
		遇到的问题:在promise内写的请求数据的代码实际上是在插件中执行，也就是this指向插件，所以需要params传参把参数传过去，
			在H5端兼容有问题（H5端promise是在本页面执行的，所以this.pageNo指向本页面，因此需要另外维护）
	*/
	import wsLm from '@/components/wsure-loadmore/loadmore.vue';
	export default {
		components:{
			wsLm
		},
		data() {
			return {
				loadCount:0,
				reloadCount:0,
				params:{
					pre:'loadmore',
				},
				title:'wsure-loadmore',
				pageSize:10,
				//#ifdef H5
				pageNo:1, // 为何要在此维护pageNo？因为在H5中，你在本页面的Promise内写的this.pageNo指向的是现在定义的这个pageNo。如果是别的平台，会指向插件内的pageNo参数，自然无需在这边维护了。
				//#endif
			}
		},
		methods: {
			//核心方法：你只需要在此实现接口调用获得数据，把数组格式的数据resolve()回去即可，大量的页面交互逻辑插件已经实现好了
			randomData(){ 
				return new Promise((resolve, reject) => {
					var res = [];
					for(var i=0;i<this.pageSize;i++){
						var s = {
							name:this.params.pre+'_'+this.pageNo+'_'+i,
						}
						res.push(s);
					}
					
					//#ifdef H5
						//h5 请手动给pageNo自增，非H5端不需要
						this.pageNo++;
					//#endif
					//模拟请求的延迟
					setTimeout(function () {
						console.log('getData');
						resolve(res);
					}, 3000);
				});
			}
		},
		//#ifndef H5
		onLoad() {
			//如果你是app或者小程序，按自己习用onLoad就好了
			console.log("onLoad")
			this.loadCount ++;
		},
		//#endif
		
		//#ifdef H5
		onReady(){
			//如果是H5，请一定使用onReady方法初次加载数据，否则不会触发
			console.log("onReady")
			this.loadCount ++;
		},
		//#endif
		
		onPullDownRefresh() {
			this.reloadCount ++;
			//#ifdef H5
				//H5 需要在下拉刷新后清零一下pageNo
				this.pageNo = 0;
			//#endif
			
			//如无特殊需要，不用在此处 uni.stopPullDownRefresh() ，插件内已经实现了
		},
		onReachBottom() {
			this.loadCount ++;;
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
