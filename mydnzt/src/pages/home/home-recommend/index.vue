<template>
    <scroll-view class='recommend_view' @scrolltolower='handleToLower' scroll-y v-if="recommends.length>0">
<!-- 推荐开始 -->
  <view class="recommend">
      <navigator
        class='recommend_item'
        v-for="(item, index) in recommends" :key="item.id"
        :url="`/pages/album/index?id=${item.target}`"
      >
        <image mode='widthFix' :src='item.thumb' ></image>
      </navigator>
  </view>
<!-- 推荐结束 -->
<!-- 月份 开始 -->
  <view class="monthes_wrap">
    <view class="monthes_title">
      <view class="monthes_title_info">
          <view class="mothes_info">
              <text> {{monthes.DD}} / </text>
              {{monthes.MM}} 月
          </view>
          <view class="moenths_text">{{monthes.title}}</view>
      </view>
      <view class="mothes_title_more">更多 > </view>
    </view>
    <view class="mothes_content">
      <view class="moenths_item"
        v-for="(item, index) in monthes.items" :key="index"
      >
        <go-detail :list="monthes.items" :index="index">
          <image mode='aspectFill' :src='item.thumb+item.rule.replace("$<Height>",360)' ></image>
        </go-detail>
      </view>
    </view>
  </view>
<!-- 月份 结束 -->
<!-- 热门 开始 -->
  <view class="hots_wrap">
    <view class="hots_title">
      <text>热门</text>
    </view>
    <view class="hots_content">
      <view class="hot_item"
        v-for="(item, index) in hotsList" :key="index"
      >
        <go-detail :list="hotsList" :index='index'>
          <image mode="aspectFill" :src='item.thumb' ></image>
        </go-detail>
      </view>
    </view>
  </view>
<!-- 热门 结束-->
  </scroll-view>
</template>
<script>
import moment from 'moment'
import goDetail from '@/components/goDetail'
export default {
  components:{
    goDetail
  },
  data(){
    return {
      // 推荐列表
      recommends:[],
      // 月份
      monthes:{},
      // 热门
      hotsList:[],
      params:{
        limit: 30, 
        order: "hot", 
        skip: 0
      },
      // 判断是否还有数据
      hasMore:true
    }
  },
  mounted() {
    uni.setNavigationBarTitle({title:'推荐'})
    this.getList()
  
  },
  methods: {
    getList(){
      this.request({
        url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
        data: this.params
      })
      .then(result => {
        console.log(result);

        if(result.res.vertical.length === 0){
          this.hasMore = false
          uni.showToast({
            title: '没有更多数据了',
            icon: 'none',
          });
        }
        
        if(this.recommends.length === 0){
          //推荐列表数据
          this.recommends = result.res.homepage[1].items
          // console.log(this.recommends)   
          // 月份
          this.monthes = result.res.homepage[2]
          this.monthes.MM = moment(this.monthes.stime).format('MM')
          this.monthes.DD = moment(this.monthes.stime).format('DD')

        }
          // 热门数据
          this.hotsList = [...this.hotsList, ...result.res.vertical]
          
      })
    },
    handleToLower(){
      // console.log(1)   
      if(this.hasMore){
        this.params.skip += this.params.limit
        this.getList()
      } else{
        // 弹窗提示用户
        uni.showToast({
          title: "没有数据了",
          icon: "none"
        });
      }
    }
  },
};
</script>

<style lang="scss" scope>
.recommend_view{
  height:calc(100vh - 36px);
}
.recommend{
    display: flex;
    flex-wrap: wrap;
    .recommend_item{
        width: 50%;
        border: 5rpx solid #fff;
    }
}
// 月份
.monthes_wrap {
  .monthes_title {
      display: flex;
      justify-content: space-between;
      padding: 20rpx;
    .monthes_title_info {
        color: $color;
        font-size: 30rpx;
        font-weight: 600;
        display: flex;
      .mothes_info {
        text {
            font-size: 36rpx;
        }
      }

      .moenths_text {
          color: #666;
          font-size: 34rpx;
          margin-left: 30rpx;
      } 
    }

    .mothes_title_more {
        font-size: 24rpx;
        color: $color;
    }
  }

  .mothes_content {
        display: flex;
        flex-wrap: wrap;
      .moenths_item{
          width: 33.333%;
          border: 5rpx solid #fff;
          image{

          }
      }
  }
}
// 热门
view.hots_wrap {
  view.hots_title {
    padding: 20rpx;
    text {
      border-left: 20rpx solid $color;
      padding-left: 20rpx;
      font-size: 34rpx;
      font-weight: 600;
    }
  }

  view.hots_content {
      display: flex;
      flex-wrap: wrap;
    view.hot_item {
      width: 33.33%;
      border: 5rpx solid #fff;
      image {
      }
    }
  }
}
</style>