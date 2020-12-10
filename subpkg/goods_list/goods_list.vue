<template>
  <view>
    <view class="goods-list">
      <view v-for="(goods, i) in goodsList" :key="i" @click="gotoDetail(goods)">
        <!-- 为 my-goods 组件动态绑定 goods 属性的值 -->
           <my-goods :goods="goods"></my-goods>
           
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 请求参数对象
        queryObj: {
          // 查询关键词
          query: '',
          // 商品分类id
          cid: '',
          // 页码值
          pagenum: 1,
          // 每页显示多少条数据
          pagesize: 10,
        },
        // 商品列表的数据
        goodsList: [],
        // 总数量，用来实现分页
        total: 0,
       // 下拉多次请求数据的节流阀
       isLoading: false
      };
    },
    onLoad(options) {

      // console.log(options)
      // 将页面参数转存到 this.queryObj 对象中
      this.queryObj.query = options.query || ''
      this.queryObj.cid = options.cid || ''
      // console.log(options)

      // 调用获取商品列表数据的方法
      this.getGoodsList()
    },

    methods: {
      // 获取数据
      async getGoodsList(cb) {
        
        // 发起请求之前打开节流阀
        this.isLoading = true
        
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
        
        if(res.meta.status !== 200) return uni.$showMsg()
        
        // 判断获取数据的时候是否有这个方法
        cb && cb()
        
        // console.log(res)
        this.goodsList = [...this.goodsList, ...res.message.goods]
        this.total = res.message.total
        
        // 获取到数据之后关闭节流阀
        this.isLoading = false
      },
      
      // 跳转到商品详情页面
      gotoDetail(goods){
        // console.log(item)
        uni.navigateTo({
          url:'/subpkg/goods_detail/goods_detail?goods_id=' + goods.goods_id
        })
      }
    },
    // 上拉触底的方法
    onReachBottom(){
      
      // 判断数据是否获取完毕
      // 当前的页码值 * 每页显示多少条数据 >= 总数条数  如果公式成立，则证明没有下一页数据了：
      if(this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('数据加载完毕！')
      
       // 判断是否正在请求其它数据，如果是，则不发起额外的请求
       if(this.isLoading) return
      
      // 让页码值 +1 页
      this.queryObj.pagenum ++
      
      // 重新获取数据
      this.getGoodsList()
      // console.log(this.queryObj.pagenum)
    },
    
    // 下拉刷新的事件
    onPullDownRefresh() {
      
      // 重置商品列表数据
      this.queryObj.pagenum = 1
      this.total = 0 
      this.isLoading = false
      this.goodsList = []
      
      // 重新发起请求, 并且关闭下拉刷新效果
      this.getGoodsList( () => uni.stopPullDownRefresh())
    }
    
    

  }
</script>

<style lang="scss">

</style>
