<template>
  <view>
    <!-- 专辑背景 -->
    <view class="album_wrap">
      <image :src='album.cover' mode='widthFix' ></image>
      <view class="album_info">
        <view class="info_name">{{album.name}}</view>
        <view class="info_btn">关注专辑</view>
      </view>
    </view>

    <!-- 专辑作者 -->
    <view class="album_author">
      <view class="album_author_info">
        <image :src='album.user.avatar' mode='widthFix'></image>
        <view class="author_info_name">{{album.user.name}}</view>
      </view>
      <view class="author_desc">
        <text>{{album.desc}}</text>
      </view>
    </view>
    <!-- 图片 -->
    <view class="album_list">
      <view class="album_item"
        v-for="(item, index) in wallpaper" :key="index"
      >
        <go-detail :list='wallpaper' :index="index">
          <image image mode='aspectFill' :src='item.thumb+item.rule.replace("$<Height>",360)'></image>

        </go-detail>
      </view>
    </view>
  </view>
</template>

<script>
import goDetail from '@/components/goDetail'
export default {
  components:{
    goDetail
  },
  data(){
    return {
      // 请求参数
      params:{
        limit: 30,
        order: "new",
        skip:0,
        first: 1,
      },
      id:-1,
      album:{},
      wallpaper:[],
      hasMore: true,
    }
  },
  onLoad(options){
    // 接收传过来的id
    // console.log(options)
    this.id = options.id
    this.getXqList()
  },
  onReachBottom(){
    if(this.hasMore){
      this.params.first = 0;
      this.params.skip += this.params.limit;
      this.getXqList()
    }else{
      uni.showToast({
        title:'没有更多的图片了',
        icon:'none'
      })
    }
  },
  mounted() {
  },
  methods:{
    getXqList(){
      this.request({
        url:`http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
        data: this.params
      }).then(result =>{
        console.log(result)
        if (Object.keys(this.album).length === 0) {
          this.album = result.res.album;
        }

        if (result.res.wallpaper.length === 0) {
          this.hasMore = false;
          uni.showToast({
            title: "没有更多的图片了",
            icon: "none"
          });
          return;
        }

        this.wallpaper = [...this.wallpaper, ...result.res.wallpaper];
      })
    }
  }
}
</script>

<style lang="scss" scope>
.album_wrap {
  position: relative;
  image {

  }

  .album_info {
    position: absolute;
    bottom:0;
    left:0;
    display: flex;
    justify-content: space-between;
    padding: 0 15rpx;
    height: 80rpx;
    width: 100%;
    align-items: center;
    color: #fff;
    .info_name {
      font-size: 40rpx;
    }

    .info_btn {
      width: 153rpx;
      height: 60rpx;
      background-color: $color;
      display: flex;
      justify-content: center;
      align-items: center;
      border-radius: 10rpx;
    }
  }
}
.album_author {
  .album_author_info {
    display:flex;
    padding: 10rpx 0;
    image {
      width: 50rpx;
    }

    .author_info_name {
      color: #000;
      margin-left: 15rpx;
    }
  }

  .author_desc {
    text {

    }
  }
}
.album_list {
  display:flex;
  flex-wrap:wrap;
  .album_item {
    width: 33.33%;
    border: 3rpx solid #fff;
    image {
      height: 160rpx;
    }
  }
}
</style>