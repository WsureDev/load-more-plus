# wsure-load-more

一个优雅的、高复用性的、适合uni-app的分页加载插件
解决了包含分页的页面需要重复写交互逻辑和分页状态等等诸多麻烦，避免代码大量重复。
现在用户配置好了子元素和获取数据的provider方法，以及相关事件，就可以轻松使用

## 使用方法

### 1.引入注册
`代码示例:`
#### ①局部引入
```javascript
    //局部引入 （需要的页面
    import wsLoadMore from '@/components/wsure-load-more/load-more.vue';
    //记得注册插件
    export default {
        components:{
            wsLoadMore
        },
    }

```
#### ②全局引入
```javascript
    //全局引入 （main.js
    import wsLoadMore from './components/wsure-load-more/load-more.vue';
    Vue.component('wsLoadMore',wsLoadMore)

```
### 2.页面模板
`代码示例:`
```vue
    <!-- 请自行改写.item，以实现你要的效果-->
    <ws-load-more ref="loadMore" @provider="provider" :pageSize="12">
        <template v-slot:list="{ items }"> <!-- 此处为插槽，只能使用template或其他自定义component -->
            <view class="solid-top" v-for="(item,index) in items" :key="index">
                <!-- 此处为每个元素的样式 -->
                <view class="item"> {{item.name}} </view>
                
            </view>
        </template>
    </ws-load-more>

```
### 3.页面逻辑
`代码示例:`
```javascript
    export default {
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
    					
    					var res = [
    					    {
    					        name:'a1'
    					    },{
                                name:'a2'
                            },{
                                name:'a3'
                            },{
                                name:'a4'
                            },
                            
                            //......
    					];
    					//向插件传入当前页数据
    					that.$refs.loadMore.pushData(res);
    				}, 1000);
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
```
### 4.pages.json配置
`代码示例:`
> 配置style的enablePullDownRefresh:true，以此开启下拉刷新
```json
{
    "pages": [ 
        {
            "path": "pages/index/index",
            "style": {
                "navigationBarTitleText": "wsure-loadmore",
                "enablePullDownRefresh": true   
            }
        }
    ]
}
```

## 参数

|参数名    |类型     |默认值    |备注  |
|----     | ---:     | :----:   | :--- |
|pageSize |Number  | 10     | 默认分页大小(请根据实际需要填写，若元素不能占满页面高度，将无法触发页面触底事件导致无法加载下一页）|
|color    |String  | #777777| 底部文字和loading icon颜色|
|reLoadData|方法| - | 用于初始化页面数据，会清空当前列表内数据并重新获得一次第一页的数据 |
|reachBottom| 方法| - | 对应触底事件，用来加载下一页的数据 |
|pullDownRefresh| 方法 | - | 对应页面的下拉刷新事件，用于初始化页面数据，会清空当前列表内数据并重新获得一次第一页的数据 |
|pushData | 方法 | - | 页面获取到当前页的数据后调用此方法将数据传回插件 |
|@provider | 事件 | - | 用于插件向页面请求数据，需要在页面中实现这个事件 |

#### @provider
|参数名    |类型     |默认值    |备注  |
|----     | ---:     | :----:   | :--- |
|pageNo |Number| 1 | 初始化默认从1开始，由插件维护其状态 |
|pageSize| Number| 10 | 默认为10，(若元素不能占满页面高度，将无法触发页面触底事件导致无法加载下一页）|