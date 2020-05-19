<template>
  <view 
    @touchstart="handleTouchStart"
    @touchend="handleTouchEnd"
  >
      <slot></slot>
  </view>
</template>

<script>
export default {
  data(){
    return {
      startTime:'',
      startX:'',
      startY:'',
    }
  },
  methods: {
    handleTouchStart(e){
      this.startTime = Date.now()
      this.startX = e.changedTouches[0].clientX
      this.startY = e.changedTouches[0].clientY


    },
    handleTouchEnd(e){
      const endTime = Date.now()
      const endX = e.changedTouches[0].clientX
      const endY = e.changedTouches[0].clientY

      let direction = ''

      if(this.startTime - endTime > 2000){
        return
      }

      if(Math.abs(endX - this.startX )> 10 && Math.abs(endY-this.startY) < 10){
        direction = endX - this.startX > 0 ?'right' : 'left'
      }else{
        return
      }

      this.$emit("swiperAction",{direction})
      // console.log(direction)
    }

  },
}
</script>

<style lang="">
  
</style>