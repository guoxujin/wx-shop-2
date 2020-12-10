<template>
  <view class="search-box">
    
    <!-- 使用 uni-ui 提供的搜索组件 -->
    <uni-search-bar @input="input" :radius="100" cancelButton="none"></uni-search-bar>

    <!-- 搜索建议列表 -->
    <view class="sugg-list" v-if="searchResults.length !== 0 ">
      <view class="sugg-item" v-for="(item, i) in searchResults" :key="i" @click="goToDetail(item)">
        <view class="goods-name">{{item.goods_name}}</view>
        <uni-icons type="arrowright" size="16"></uni-icons>
      </view>
    </view>

    <!-- 搜索历史记录 -->
    <view class="history-box" v-else>
      <!-- 标题区域 -->
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="17" @click="cleanHistory"></uni-icons>
      </view>
      <!-- 列表区域 -->
      <view class="history-list">
        <uni-tag :text="item" v-for="(item, i ) in historys" :key="i" @click="goToGoodsList(item)"></uni-tag>
      </view>
    </view>

  </view>
</template>

<script>
  export default {
    // 数据
    data() {
      return {
        // 防抖
        timer: null,
        // 搜素的关键词
        kw: '',
        // 搜索到的数据
        searchResults: [],
        // 搜索历史的数组
        historyList: []
      };
    },
    // 生命周期函数
    onLoad() {
      this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
    },
    // 方法
    methods: {

      // 搜索框的输入事件
      input(e) {

        clearTimeout(this.timer)

        this.timer = setTimeout(() => {
          // console.log(e.value)
          this.kw = e.value

          // 根据关键词，查询搜索建议列表
          this.getSearchList()

        }, 500)
      },

      // 根据搜索关键词，搜索商品建议列表
      async getSearchList() {
        // 判断关键词是否为空
        if (this.kw.length === 0) {
          this.searchResults = []
          return
        }

        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/qsearch', {
          query: this.kw
        })
        // console.log(res)
        if (res.meta.status !== 200) return uni.$showMsg()

        this.searchResults = res.message

        // 1. 查询到搜索建议之后，调用 saveSearchHistory() 方法保存搜索关键词
        this.saveSearchHistory()
      },

      // 点击搜索出来的每一项跳转到商品详情页
      goToDetail(item) {
        // console.log(item.goods_id)
        uni.navigateTo({
          url: '/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
        })
      },

      // 保存历史搜索记录
      saveSearchHistory() {
        
        // 2.1 直接把搜索关键词 push 到 historyList 数组中
        this.historyList.push(this.kw)
        
         // 1. 将 Array 数组转化为 Set 对象
        const set = new Set(this.historyList)
        // 2. 调用 Set 对象的 delete 方法，移除对应的元素
        set.delete(this.kw)
        // 3. 调用 Set 对象的 add 方法，向 Set 中添加元素
        set.add(this.kw)
        
        // 4. 将 Set 对象转化为 Array 数组
        this.historyList = Array.from(set)
        
        // 对搜索历史记录 进行搜索持久化的存储
        uni.setStorageSync('kw', JSON.stringify(this.historyList))
      },
      
      // 清空搜索记录
      cleanHistory(){
        // 清空 data 中保存的搜索历史
        this.historyList = []
        // 清空本地存储中记录的搜索历史
        uni.setStorageSync('kw', '[]')
      },
      
      goToGoodsList(kw) {
        // console.log(item)
        uni.navigateTo({
          url:'/subpkg/goods_list/goods_list?query=' + kw
        })
      }
      
      
    },
    // 计算属性
    computed: {
      historys() {
        return [...this.historyList].reverse()
      }
    }
  }
</script>

<style lang="scss">
  .search-box {
    // 设置盒子 吸顶效果
    position: sticky;
    top: 0;
    z-index: 9999;

    .sugg-list {
      padding: 0 5px;

      .sugg-item {
        font-size: 12px;
        padding: 13px 0;
        border-bottom: 1px solid #efefef;
        display: flex;
        align-items: center;
        justify-content: space-between;

        .goods-name {
          // 设置文字不允许换行
          white-space: nowrap;

          // 溢出部分隐藏
          overflow: hidden;

          // 文本溢出后, 使用 ... 代替
          text-overflow: ellipsis;

          margin-right: 3px;

        }
      }

    }

    .history-box {
      padding: 0 5px;

      .history-title {
        display: flex;
        align-items: center;
        justify-content: space-between;
        height: 40px;
        font-size: 13px;
        border-bottom: 1px solid #efefef;
      }

      .history-list {
        display: flex;
        flex-wrap: wrap;

        .uni-tag {
          margin: 5px 5px 0 0;
        }
      }
    }

  }
</style>
