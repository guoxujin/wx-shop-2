<template>
  <view>
    
    <!-- 搜索区域 -->
    <view class="search-box">
        <my-search @click="gotoSearch"></my-search>
    </view>
   
    <!-- 轮播图区域 -->
    <swiper indicator-dots autoplay :interval="3000" :duration="1000" circular>

      <swiper-item v-for="(item, i) in swiperList" :key="i">
        <navigator class="swiper-item">
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>

    </swiper>
    <!-- / 轮播图区域 -->

    <!-- 分类导航区域 -->
    <view class="nav-list">
      <view class="nav-item" v-for="(item, i ) in navList" :key="i" @click="navClickHandler(item)">
        <image :src="item.image_src" class="nav-image"></image>
      </view>
    </view>
    <!-- / 分类导航区域 -->

    <!-- 楼层区域 -->
    <view class="floor-list">
      <view class="floor-item" v-for="(item, i) in floorList" :key="i">
        <!-- 楼层的标题 -->
        <image :src="item.floor_title.image_src" class="floor-title"></image>
        <!-- 楼层的图片区域 -->
        <view class="floor-img-box">
          <!-- 左侧大图片的盒子 -->
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width:item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
          </navigator>
          <!-- 右侧4个小图片的盒子 -->
          <view class="right-img-box">
            <navigator class="right-item-box" v-for="(item2, i2) in item.product_list" :key="i2" v-if="i2 !== 0" :url="item2.url">
              <image :src="item2.image_src" mode="widthFix" :style="{width: item2.image_width + 'rpx'}"></image>
            </navigator>
          </view>
        </view>
      </view>
    </view>
    <!-- /楼层区域 -->

  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 存放轮播的数据
        swiperList: [],
        // 分类导航的数据列表
        navList: [],
        // 楼层的数据
        floorList: []
      };
    },
    onLoad() {
      // 获取轮播图的数据
      this.getSwiperList()
      // 2. 在 onLoad 中调用获取数据的方法
      this.getNavList()
      // 获取楼层的树
      this.getFloorList()
    },
    methods: {
      // 获取轮播图数据列表
      async getSwiperList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/swiperdata')
        if (res.meta.status !== 200) return uni.$showMsg()
        this.swiperList = res.message
        // uni.$showMsg('数据请求成功')
      },

      // 获取分类导航的数据
      async getNavList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/catitems')
        if (res.meta.status !== 200) return uni.$showMsg()
        // console.log(res)
        this.navList = res.message
      },

      // 点击分类导航进行跳转
      navClickHandler(item) {
        console.log(item)
        if (item.name === '分类') {
          uni.switchTab({
            url: '/pages/cate/cate'
          })
        }
      },

      // 获取楼层的数据
      async getFloorList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/floordata')
        // console.log(res)
        if (res.meta.status !== 200) return uni.$showMsg()

        // 对数据进行处理
        res.message.forEach(floor => {
          floor.product_list.forEach(prod => {
            prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[
              1]
          })
        })

        this.floorList = res.message
      },
      
      //  跳转到 搜索分包页面
      gotoSearch(){
        // console.log(123)
        uni.navigateTo({
          url:'/subpkg/search/search'
        })
      },
    }
  }
</script>

<style lang="scss">
  swiper {
    height: 330rpx;

    .swiper-item,
    image {
      width: 100%;
      height: 100%;
    }
  }

  .nav-list {
    display: flex;
    justify-content: space-around;
    margin: 15px 0;

    .nav-image {
      width: 128rpx;
      height: 140rpx;
    }
  }

  .floor-title {
    width: 100%;
    height: 60rpx;
  }

  .right-img-box {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }

  .floor-img-box {
    display: flex;
    padding-left: 10rpx;
  }
  
  .search-box {
    // 设置盒子 吸顶效果
    position: sticky;
    top: 0;
    z-index: 9999;
  }
</style>
