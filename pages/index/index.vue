<template>
	<view>
		<ws-load-more ref="loadMore" @provider="provider" :pageSize="12" color="#66ccff">
			<template v-slot:list="{ items }"> <!-- 此处为插槽，只能使用template或其他自定义component -->
				<view class="solid-top" v-for="(item,index) in items" :key="index">
					<view class="item"> {{item.name}} </view>
				</view>
			</template>
		</ws-load-more>
	</view>
</template>

<script>
	import wsLoadMore from '@/components/wsure-load-more/load-more.vue';
	export default {
		components:{
			wsLoadMore
		},
		data() {
			return {
				pre:'item',
				failFlag:false,
				count:0
			}
		},
		methods: {
			/**核心 只需实现@provider事件
				入参：e包含两个参数：pageNo和pageSize
				请求完成后填充数据：this.$refs.loadMore.pushData(res)
					成功:在获取数据成功后调用this.$refs.loadMore.pushData(res) res为数组，
					失败:在获取数据失败后调用this.$refs.loadMore.pushData(res) res为null，
			*/
			provider(e){
				console.log(e);
				var that = this;
				//模拟网络请求的延迟
				setTimeout(function () {
					
					var res = that.getData(e);
					
					console.log("pushData!")
					that.$refs.loadMore.pushData(res);
					
				}, 1000);
			},
			//为了演示功能编写的演示数据生成逻辑
			getData(e){
				//模拟接口返回的第pageNo页的页数据
				var res = [];
				for(var i=0;i<e.pageSize;i++){
					var s = {
						name:this.pre+'_'+e.pageNo+'_'+i,
					}
					res.push(s);
				}
				//模拟请求数据失败
				if(!this.failFlag && e.pageNo == 3){
					
					uni.showModal({
						title: '模拟网络请求失败',
						content: '模拟网络请求失败，未获取到数据\n再次上拉即可',
						showCancel: false,
						cancelText: '',
						confirmText: '确定',
						success: res => {},
						fail: () => {},
						complete: () => {}
					});
					
					res = null; // 失败时请传null
					this.failFlag = true; // 清除模拟失败标志
				}
				
				//模拟尾页数据（尾页的数据可能一直在增加，但是不满一页）
				if(e.pageNo == 5 )
				{
					uni.showModal({
						title: '模拟请求尾页',
						content: '清多次上拉模拟陆续有新数据加载',
						showCancel: false,
						cancelText: '',
						confirmText: '确定',
						success: res => {},
						fail: () => {},
						complete: () => {}
					});
					
					//模拟每次请求时，尾页新增了2条数据
					if(this.count <e.pageSize){
						this.count += 2; // 尾页总数据条数增加
					}
					
					//模拟尾页的数据
					res = [];
					for(var i=0;i<this.count;i++){
						var s = {
							name:this.pre+'_'+e.pageNo+'_'+i,
						}
						res.push(s);
					}
				}
				//返回当前这一页的数据（成功返回数组，失败返回null）
				return res;
			}
		},
		
		//#ifndef H5
		onLoad() {
			//如果你是`非H5`平台（app-plus或者小程序），按自己习用onLoad就好了
			console.log("onLoad")
			this.$refs.loadMore.reLoadData()
		},
		//#endif
		
		//#ifdef H5
		onReady(){
			//如果是H5，请一定使用onReady方法初次加载数据，否则不会触发
			console.log("onReady")
			this.$refs.loadMore.reLoadData()
		},
		//#endif
		
		onPullDownRefresh() {
			console.log("onPullDownRefresh")
			this.$refs.loadMore.pullDownRefresh()
		},
		onReachBottom() {
			console.log("onReachBottom")
			this.$refs.loadMore.reachBottom()
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
