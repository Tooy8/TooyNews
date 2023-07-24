<template>
	<view>

		<scroll-view scroll-x class="navscroll">

			<view class="item" :class="index == navIndex ? 'active':''" v-for="(item,index) in navArr " :key=item.id
				@click="clickNav(index,item.id)">{{item.classname}}</view>

		</scroll-view>

		<view class="content">
			<div class="row">
				<view v-for="item in newsArr" :key="item.id" class="news">
					<newsbox :item="item" @click.native="goDetail(item)"></newsbox>
				</view>
			</div>
		</view>

<view class="nodata" v-if="!this.newsArr.length">
	<image src="../../static/images/nodata.png" mode="widthFix"></image> 
</view>

<view class="loading" v-if="newsArr.length">			
			<view v-if="loading==1">数据加载中...</view>
			<view v-if="loading==2">没有更多了~~</view>
		</view>
		
	</view>

</template>

<script>
	export default {
		data() {
			return {
				navIndex: 0,
				navArr: [],
				newsArr:[],
				currentPage:1,
				loading:0
			}
		},
		onLoad() {
			this.getNavData()
			this.getNewsdata()
		},
		onReachBottom() {
			if(this.loading==2){
				return;
			}
			this.currentPage++
			this.loading=1;
			this.getNewsdata()
		},
		methods: {
			clickNav(index,id) {
				this.navIndex = index
				this.currentPage = 1
				this.newsArr = []
				this.loading = 0
				this.getNewsdata(id)
			},
			goDetail(item) {
				uni.navigateTo({
					url: `/pages/detail/detail?cid=${item.classid}&id=${item.id}`
				})
			},
			//导航栏数据
			getNavData() {
				uni.request({
					url: "https://ku.qingnian8.com/dataApi/news/navlist.php",
					
					success: res => {
						this.navArr = res.data
					}
				})
			},
			// 新闻数据
			getNewsdata(id = 50){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/newslist.php",
					data:{
						cid:id,
						page:this.currentPage
					},
					success:res=>{
						if(res.data.length==0){
							this.loading=2
						}
						this.newsArr =[...this.newsArr,...res.data] 
					}
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.navscroll {
		height: 100rpx;
		background: #F7F8FA;
		white-space: nowrap;
		position: fixed;
		top: var(--window-top);
		left: 0;
		z-index: 10;

		/deep/ ::-webkit-scrollbar {
			width: 4px !important;
			height: 1px !important;
			overflow: auto !important;
			background: transparent !important;
			-webkit-appearance: auto !important;
			display: block;
		}

		.item {
			font-size: 40rpx;
			display: inline-block;
			line-height: 100rpx;
			padding: 0 30rpx;
			color: #333;

			&.active {
				color: #31C27C;
			}
		}
	}

	.content {
		padding: 30rpx;
		padding-top: 130rpx;

		.row {
			border-bottom: 1px dotted #efefef;
			padding: 20rpx 0;
			.news{
				margin-bottom: 40rpx;
			}
		}
	}
	.nodata{
		display: flex;
		justify-content: center;
		image{
			width: 360rpx;
		}
	}
	.loading{
		text-align: center;
		font-size: 26rpx;
		color:#888;
		line-height: 2em;
	}
</style>