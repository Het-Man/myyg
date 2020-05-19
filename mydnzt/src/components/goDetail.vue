<template>
  <view @click='handleClick' >
    <slot></slot>
  </view>
</template>

<script>
export default {
  props:{
    list:Array,
    index:Number
  },
  mounted() {
    
  },
  
  methods: {
    handleClick(){
      getApp().globalData.imgList = this.list;
      getApp().globalData.imgIndex = this.index;

      uni.navigateTo({
        url:'/pages/imgDetail/index'
      })
    }
  },
}
</script>

<style lang="">
  
</style>