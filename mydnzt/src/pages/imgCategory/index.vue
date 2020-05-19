<template>
  <view class='home_tab'>
    <view class="home_tab_title">
        <view class="title_inner">
          <uni-segmented-control 
            :current="current" 
            :values="items.map(v => v.title)" 
            @clickItem="onClickItem" 
            style-type="text" 
            active-color="#d4237a">
          </uni-segmented-control>
        </view>
        <view class="iconfont  iconsearch"></view>
    </view>
    
    <scroll-view enable-flex @scrolltolower='handleScrolltolower' scroll-y class="content">
      <view class="cate_item"
        v-for="(item, index) in vertical" :key="index"
      >
        <image mode='widthFix' :src='item.thumb' ></image>
      </view>
    </scroll-view>
  </view>
</template>
<script>
import { uniSegmentedControl } from "@dcloudio/uni-ui";
export default {
  components: {
    uniSegmentedControl
  },
  data() { 
    return {
      items: [
        {title:'最新',order:'new'},
        {title:'热门',order:'hot'},
      ],
      params:{
        limit:30,
        order:'new',
        skip:0
      },
      current: 0,
      id:'',
      // 页面显示数据的数组
      vertical:[],
      hasMore:true
    };
  },
  onLoad(options){
    console.log(options)
    this.id = options.id
    this.getList()
  },
  methods: {
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;
      }else {
        return
      }
      this.params.order = this.items[e.currentIndex].order;
      this.params.skip = 0;
      this.vertical = [];
      this.getList()
    },
    getList(){
      this.request({
        url:`http://157.122.54.189:9088/image/v1/vertical/category/${this.id}/vertical`,
        data:this.params
      })
      .then(result =>{
        console.log(result);
        if(result.res.vertical.length === 0){
          this.hasMore =false
          uni.showToast({
            title:"没有更多数据了",
            icon:"none"
          })
          return;
        }
        this.vertical =[...this.vertical,...result.res.vertical];
        
      })
    },
    handleScrolltolower(){
      if(this.hasMore){
        this.params.skip+=this.params.limit;
        this.getList();

      }else{
        uni.showToast({
          title:'没有更多数据了',
          icon:'none'
        })
      }
    }
  },
};
</script>

<style lang="scss">
.home_tab_title{
  position: relative;
  .title_inner{
    width: 60%;
    margin: 0 auto;
  }
  .iconsearch{
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    right: 5%;
  }

}
.content{
  display:flex;
  flex-wrap: wrap;
  height: calc(100vh - 36px);
  .cate_item{
    width: 33.333%;
    border: 5rpx solid #fff;
  }
}
</style>