<template>
  <view v-if="goods_info.goods_name" class="goods-detail-container">

    <!-- 商品详情轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" circular>
      <swiper-item v-for="(item, i ) in goods_info.pics" :key="i">
        <image :src="item.pics_big" @click="preview(i)"></image>
      </swiper-item>
    </swiper>

    <!-- 商品信息区域 -->
    <view class="goods-info-box">
      <!-- 商品价格 -->
      <view class="price">￥{{goods_info.goods_price}} </view>
      <!-- 商品信息主体区域 -->
      <view class="goods-info-body">
        <!-- 商品名称 -->
        <view class="goods-name">{{goods_info.goods_name}} </view>
        <!-- 收藏 -->
        <view class="favi">
          <uni-icons type="star" size="18" color="gray"></uni-icons>
          <text>收藏</text>
        </view>
      </view>
      <!-- 运费 -->
      <view class="yf">快通 : 免运费</view>
    </view>

    <rich-text :nodes="goods_info.goods_introduce"></rich-text>

    <!-- 商品导航组件区域 -->
    <view class="goods_nav">
      <!-- fill 控制右侧按钮的样式 -->
      <!-- options 左侧按钮的配置项 -->
      <!-- buttonGroup 右侧按钮的配置项 -->
      <!-- click 左侧按钮的点击事件处理函数 -->
      <!-- buttonClick 右侧按钮的点击事件处理函数 -->
      <uni-goods-nav :fill="true" :options="options" :buttonGroup="buttonGroup" @click="onClick" @buttonClick="buttonClick" />
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 商品详情对象
        goods_info: {},

        options: [ {
          icon: 'shop',
          text: '店铺',
          infoBackgroundColor: '#007aff',
          infoColor: "red"
        }, {
          icon: 'cart',
          text: '购物车',
          info: 2
        }],
        buttonGroup: [{
            text: '加入购物车',
            backgroundColor: '#ff0000',
            color: '#fff'
          },
          {
            text: '立即购买',
            backgroundColor: '#ffa200',
            color: '#fff'
          }
        ]
      };
    },
    onLoad(options) {
      // console.log(options)
      // 获取商品 Id
      const goods_id = options.goods_id
      // 调用请求商品详情数据的方法
      this.getGoodsDetail(goods_id)
    },
    methods: {
      // 定义请求商品详情数据的方法
      async getGoodsDetail(goods_id) {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/detail', {
          goods_id
        })
        console.log(res)
        if (res.meta.status !== 200) return uni.$showMsg()

        // 使用字符串的 replace() 方法，为 img 标签添加行内的 style 样式，从而解决图片底部空白间隙的问题    replace(/webp/g, 'jpg') 为了解决  IOS 设备不支持  webp 这种图片后缀名 所以要替换成 jpg
        res.message.goods_introduce = res.message.goods_introduce.replace(/<img /g, '<img style="display:block;" ').replace(
          /webp/g, 'jpg')

        // 为 data 中的数据赋值
        this.goods_info = res.message
      },

      // 点击图片 实现图片的预览
      preview(i) {
        // console.log(i)
        // 调用 uni.previewImage() 方法预览图片
        uni.previewImage({
          // 预览时，默认显示图片的索引
          current: i,
          // 所有图片 url 地址的数组
          urls: this.goods_info.pics.map(x => x.pics_big)
        })
      },
      
      // 左侧按钮的点击事件处理函数
      onClick(e){
        // console.log(e)
        if(e.content.text === '购物车'){
          // 切换到 购物车页面
          uni.switchTab({
            url:'/pages/cart/cart'
          })
        }
      }
    }
    
  
  }
</script>

<style lang="scss">
  swiper {
    height: 750rpx;

    image {
      width: 100%;
      height: 100%;
    }
  }

  .goods-info-box {
    padding: 10px;
    padding-right: 0;

    // 价格
    .price {
      color: #c00000;
      font-size: 18px;
      margin: 10px 0;
    }

    // 商品信息区域
    .goods-info-body {
      display: flex;
      justify-content: space-between;

      // 商品名称
      .goods-name {
        font-size: 13px;
        padding-right: 10px;
      }

      // 收藏
      .favi {
        width: 120px;
        font-size: 12px;
        display: flex;
        flex-direction: column;
        align-items: center;
        border-left: 1px solid #efefef;
        color: gray;
      }
    }

    .yf {
      margin: 10px 0;
      font-size: 12px;
      color: gray;
    }
  }

  .goods_nav {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
  }

  .goods-detail-container {
    // 给页面外层的容器，添加 50px 的内padding，
    // 防止页面内容被底部的商品导航组件遮盖
    padding-bottom: 50px;

  }
</style>
