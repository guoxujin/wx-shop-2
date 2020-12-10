<template>
	<view class="my-search-container" :style="{'background-color' : bgcolor}" @click="searchBoxHandler">
		<view class="my-search-box" :style="{'border-radius' : radius + 'px'}">
      <!-- 使用 uni-ui 提高的图标组件 -->
		  <uni-icons type="search" size="17"></uni-icons>
      <text class="placeholder">搜索</text>
		</view>
	</view>
</template>

<script>
	export default {
    props: {
      // 背景颜色
      bgcolor: {
        type: String,
        default:'#C00000'
      },
      // 圆角尺寸
      radius: {
        type:Number,
        default:18
      }
    },
		data() {
			return {
				
			};
		},
    methods:{
      searchBoxHandler(){
        console.log(1111)
        
        // 可以触发 组件上直接绑定的 事件
        this.$emit('click')
      }
    }
	}
</script>

<style lang="scss">
.my-search-container {
  // background-color: #C00000;
  height: 50px;
  display: flex;
  align-items: center;
  padding: 0 15px;
  
  .my-search-box {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 36px;
    background-color: #fff;
    // border-radius: 18px;
    width: 100%;
    
    .placeholder {
      font-size: 15px;
      margin-left: 5px;
    }
  }
 
}
</style>
