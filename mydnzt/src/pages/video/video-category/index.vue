<template>
  <scroll-view scroll-y class='video_main' enable-flex  @scrolltolower='handleScrolltolower' >
    <view class="video_item"
      v-for="(item, index) in videowp" :key="index"
      @click='handleClick(item)'
    >
      <image mode='widthFix' :src="item.img" ></image>
    </view>
  </scroll-view>
</template>
<script>
export default {
  data(){
    return {
      videowp:[],
      hasMore:true,

    }
  },
  props:{
    urlObj:Object,
  },
  onLoad(){
  },
  watch:{
  },
  created(){

    console.log(this.urlObj)
  },
  mounted(){
    this.getList()
  },
  methods:{
    getList(){
      this.request({
        url:this.urlObj.url,
      })
      .then(result =>{
        if(result.res.videowp.length === 0) {
          this.hasMore = false;
          uni.showToast({
            title:'没有更多的图片了',
            icon:'none'
          })
        }
        this.videowp = [...this.videowp,...result.res.videowp]
      })
    },
    handleScrolltolower(){
      if(this.hasMore){
        this.urlObj.params.skip+= this.urlObj.params.limit
        this.getList()
      }else {
        uni.showToast({
            title:'没有更多的图片了',
            icon:'none'
          })
      }
    },
    handleClick(item){
      getApp().globalData.video = item

      uni.navigateTo({
        url:'/pages/videoPlay/index'
      })
    }
  }
}
</script>

<style lang="scss">
.video_main{
  display: flex;
  flex-wrap: wrap;
  height:calc(100vh - 36px);
  .video_item{
    width: 33.33%;
    border: 5rpx solid #fff;
    image{

    }
  }
}
</style>