<template>
  <view>

    <!-- 搜索区域 -->
    <!-- <my-search :bgcolor="'pink'" :radius="3"></my-search> -->
    <my-search @click="gotoSearch"></my-search>

    <view class="scroll-view-container">
      <!-- 左侧滚动视图区域 -->
      <scroll-view scroll-y="true" :croll-top="scrollTop" :style="{height:wh + 'px'}" class="left-scrool-view">
        <!-- 一级分类 -->
        <block v-for="(item, i) in cateList" :key="i">
          <view :class="['left-scroll-view-item', i === active ? 'active' : '']" @click="activeChange(i)">{{item.cat_name}}</view>
        </block>

      </scroll-view>
      <!-- 右侧滚动视图区域 -->
      <scroll-view scroll-y="true" :style="{height:wh + 'px'}" class="right-scrool-view">

        <view class="cate-lv2" v-for="(item2, i2) in cateLevel2" :key="i2">
          <!-- 二级分类的标题 -->
          <view class="cate-lv2-title">
            / {{item2.cat_name}} /
          </view>
          <!-- 当前二级分类下的三级分类 -->
          <view class="cate-lv3-list">
            <view class="cate-lv3-item" v-for="(item3, i3) in item2.children" :key="i3" @click="goToGoodsList(item3)">
              <!-- 三级分类的图片 -->
              <image :src="item3.cat_icon" mode=""></image>
              <!-- 三级分类的文本 -->
              <text>{{item3.cat_name}}</text>
            </view>
          </view>
        </view>

      </scroll-view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 当前设备可用的高度
        wh: 0,
        // 分类列表的数据
        cateList: [],
        // 被激活的项
        active: 0,
        // 二级分类的列表数据
        cateLevel2: [],
        // 滚动头的位置
        scrollTop: 0
      };
    },
    onLoad() {
      // 获取设备信息
      const sysInfo = uni.getSystemInfoSync()
      // console.log(sysInfo)
      // 屏幕的可用高度
      // 需要减去 搜索框的高度
      this.wh = sysInfo.windowHeight - 50

      // 获取分类列表的数据
      this.getCateList()
    },
    methods: {
      // 获取分类列表的数据
      async getCateList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/categories')
        // console.log(res)
        if (res.meta.status !== 200) return uni.$showMsg()
        this.cateList = res.message

        // 为二级分类列表赋值
        this.cateLevel2 = res.message[0].children

      },

      // 分类的改变事件
      activeChange(i) {
        // console.log(i)
        // 激活当前选中的那一项
        this.active = i

        // 重新为 二级分类赋值
        this.cateLevel2 = this.cateList[i].children

        // 滚动条的位置
        this.scrollTop = this.scrollTop === 0 ? 1 : 0
      },
      
      // 点击三级分类的图片 跳转到 商品页面
      goToGoodsList(item3) {
        // console.log(item3)
        uni.navigateTo({
          url: '/subpkg/goods_list/goods_list?cid=' + item3.cat_id
        })
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
  .scroll-view-container {
    display: flex;

    .left-scrool-view {
      width: 120px;

      .left-scroll-view-item {
        background-color: #F7F7F7;
        line-height: 60px;
        text-align: center;
        font-size: 12px;

        &.active {
          background-color: #ffffff;
          position: relative;

          &::before {
            content: '';
            display: flex;
            width: 3px;
            height: 30px;
            background-color: green;
            position: absolute;
            top: 50%;
            left: 0;
            transform: translateY(-50%);
          }
        }
      }
    }
  }

  .cate-lv2-title {
    font-size: 12px;
    font-weight: bold;
    text-align: center;
    padding: 15px 0;
  }

  .cate-lv3-list {
    display: flex;
    flex-wrap: wrap;

    .cate-lv3-item {
      width: 33.33%;
      display: flex;
      align-items: center;
      justify-content: center;
      flex-direction: column;
      margin-bottom: 10px;

      image {
        width: 60px;
        height: 60px;
      }

      text {
        // display: block;
        font-size: 12px;
      }
    }
  }
</style>
