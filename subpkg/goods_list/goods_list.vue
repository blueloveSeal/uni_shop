<template>
  <view class="goods-list">
    <view v-for="(item,index) in goodsList" :key="index" @click="gotoDetail(item)">
		<my-goods :goods="item"></my-goods>
	</view>
  </view>
</template>

<script>
	import MyGoods from '../../components/my-goods/my-goods.vue'
  export default {
	components: {
		MyGoods
	},
    data() {
      return {
        queryObj: {
			query: '',
			cid: '',
			pagenum: 1,
			pagesize: 10
		},
		goodsList: [],
		total: 0,
		isLoading: false,
		defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
      }
    },
	onLoad(options) {
		this.queryObj.query = options.query || ''
		this.queryObj.cid = options.cid || ''
		this.getGoodsList()
	},
	methods: {
		async getGoodsList (cb) {
			this.isLoading = true
			const { data: res } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
			if (res.meta.status !== 200) return uni.$showMsg()
			cb&&cb()
			// 为数据赋值
			this.goodsList = [...this.goodsList,...res.message.goods]
			this.total = res.message.total
			this.isLoading = false
		},
		gotoDetail (item) {
			uni.navigateTo({
				url:'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
			})
		},
	},
	onReachBottom() {
		if(this.isLoading) return
		if (this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('数据加载完毕！')
		this.queryObj.pagenum +=1
		this.getGoodsList()
	},
	onPullDownRefresh() {
		this.queryObj.pagenum = 1
		this.total = 0
		this.isloading = false
		this.goodsList = []
		this.getGoodsList(() => uni.stopPullDownRefresh())
	},
	
  }
</script>

<style lang="scss">
.goods-item {
	display: flex;
	padding: 10px 5px;
	border-bottom: 1px solid #f0f0f0;
	.goods-item-left {
		margin-right: 5px;
		.goods-pic {
			width: 100px;
			height: 100px;
			display: block;
		}
	}
	.goods-item-right {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		.goods-name {
			font-size: 13px;
		}
		.goods-price {
			font-size: 16px;
			color: #c00000;
		}
	}
}
</style>
