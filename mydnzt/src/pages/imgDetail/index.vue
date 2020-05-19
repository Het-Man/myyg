<template>
  <view>
      <!-- 用户信息 -->
      <view class="user_info" v-if="imgDetail.user" >
        <view class="user_icon">
          <image :src='imgDetail.user.avatar' mode='widthFix'></image>
        </view>
        <view class="user_desc">
          <view class="user_name">{{imgDetail.user.name}}</view>
          <view class="user_time">{{imgDetail.cnTime}}</view>
        </view>
      </view>
      <!-- 高清 大图 -->
      <view class="high_img">
        <swiper-action @swiperAction='handleSwiperAction' >

        <image :src='imgDetail.thumb' mode='widthFix'></image>
        </swiper-action>
      </view>
      <!-- 点赞 -->
      <view class="user_rank">
        <view class="ran">
          <text class='iconfont icondianzan'>{{imgDetail.rank}}</text>
        </view>
        <view class="user_collect">
          <text class='iconfont iconshoucang'>收藏</text>
        </view>
      </view>
      <!-- 专辑 -->
      <view class="album_wrap" v-if="album.length">
        <!-- 标题 -->
        <view class="album_title">相关</view>
        <!-- 内容 -->
        <view class="album_list">
          <view
            class="album_item"
            v-for="item in album"
            :key="item.id"
          >

            <view class="album_cover">
              <image
                :src="item.cover"
                mode="aspectFill"
              ></image>
            </view>
            <!-- 右边 -->
            <view class="album_info">
              <view class="album_info_text">专辑</view>
              <view class="album_name">{{item.name}}</view>
              <text class="iconfont iconiconfontjiantou4"></text>
            </view>
          </view>
        </view>
      </view>
      <!-- 最热评论 -->
      <view class="comment_hot" v-if='hot.length'>
        <view class="comment_title">
          <text class='iconfont iconhot1' ></text>
          <text class='comment_text' >最热评论</text>
        </view>
        <view class="comment_list">
          <view class="comment_item" v-for="(item, index) in hot" :key="index">

          </view>
        </view>
        <view class="commetn_user">
          <!-- 用户头像 -->
          <view class="user_icon">
            <image mode='widthFix' :src='item.user.avatar' ></image>
          </view>
          <!-- 用户信息 -->
          <view class="user_name">
            <view class="user_nickname">{{item.user.name}}</view>
            <view class="user_time">{{item.aTime}}</view>
          </view>
          <!-- 徽章 -->
          <view class="user_badge">
            <image v-for="(item2, index2) in itenm.user.title" :key="index2" :src='item2.icon' ></image>
          </view>
        </view>
        <!-- 用户评论 -->
        <view class="comment_desc">
          <view class="comment_content">{{item.content}}</view>
          <view class="comment_like">
              <text class='iconfont icondianzan'>{{item.size}}</text>
          </view>
        </view>
      </view>
      <!-- 最新评论 -->
      <view class="comment_hot" v-if='comment.length'>
        <view class="comment_title">
          <text class='iconfont iconpinglun' ></text>
          <text class='comment_text' >最新评论</text>
        </view>
        <view class="comment_list">
          <view class="comment_item" v-for="(item, index) in comment" :key="index">
            <view class="commetn_user">
              <!-- 用户头像 -->
              <view class="user_icon">
                <image mode='widthFix' :src='item.user.avatar' ></image>
              </view>
              <!-- 用户信息 -->
              <view class="user_name">
                <view class="user_nickname">{{item.user.name}}</view>
                <view class="user_time">{{item.atime}}</view>
              </view>
              <!-- 徽章 -->
              <view class="user_badge">
                <image v-for="(item2, index2) in itenm.user.title" :key="index2" :src='item2.icon' ></image>
              </view>
            </view>
            <!-- 用户评论 -->
            <view class="comment_desc">
              <view class="comment_content">{{item.content}}</view>
              <view class="comment_like">
                  <text class='iconfont icondianzan'>{{item.size}}</text>
              </view>
            </view>
          </view>

        </view>
      </view>
      <!-- 下载按钮 -->
      <view class="download">
        <view class="download_btn" @click='handleDownload'>
          下载图片
        </view>
      </view>
  </view>
</template>

<script>
import moment from 'moment'
import swiperAction from '@/components/swiperAction'
moment.locale('zh-cn')

export default {
  components:{
    swiperAction
  },
  data(){
    return {
      imgDetail:{},
      album:[],
      comment:[],
      hot:[],
      imgIndex:'',
    }
  },
  onLoad(){
    console.log(getApp().globalData);
    const {imgIndex} = getApp().globalData;
    this.imgIndex = imgIndex

    this.setData()
  },
  methods: {
    // 赋值
    setData(){
      const { imgList } = getApp().globalData;
       this.imgDetail = imgList[this.imgIndex];
      this.imgDetail.cnTime = moment(this.imgDetail.t * 1000).fromNow();
      this.getList(this.imgDetail.id)
    },
    getList(id){
      this.request({
        url:`http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`
      }).then(result =>{
        console.log(result)
        this.album = result.res.album;
        this.hot = result.res.hot;
        this.comment  = result.res.comment;
      })

    },
    handleSwiperAction(e){
      /* 
     1 用户 左滑 imgIndex++  
     2 用户 右滑 imgIndex-- 
     3 判断 数组是否越界问题！！
     4 左滑 e.direction==="left"&&this.imgIndex<imgList.length-1 
     5 右滑 e.direction==="right"&&this.imgIndex>0
      */
     const { imgList } = getApp().globalData;
     if(e.direction === 'left'&& this.imgIndex < imgList.length -1){
       this.imgIndex++
       this.setData()
     }else if(e.direction === 'right' && this.imgIndex > 0){
       this.imgIndex--
        this.setData()
     }else{
       uni.showToast({
         title:'没有更多的图片了',
         icon:'none'
       })
     }

    },
    // 下载图片
    async handleDownload(){
      // uni.downloadFile
      // uni.saveImageToPhotosAlbum
      await uni.showLoading({
        title:"下载中"
      })

      // 将远程的文件下载到小程序中 地址存到 tmepFilePath中
      let result = await uni.downloadFile({url:this.imgDetail.img})
      const { tempFilePath } = result[1]
      // 将小程序内存中的临时文件下载到本地上
      const result2 = await uni.saveImageToPhotosAlbum({
        filePath: tempFilePath
      });
     console.log(result)
     console.log(result2)
     if(result2[0] === null){
        uni.hideLoading();
        await uni.showToast({
          title:"下载成功"
          // icon
        })
     }
    }
  },

}
</script>

<style lang="scss">
.user_info {
  display:flex;
  padding: 20rpx;
  .user_icon {
    image {
      width: 88rpx;
      border-radius: 50%;
    }
  }

  .user_desc {
    padding-left: 20rpx;
    .user_name {
      color:#000;
      font-weight: 600;
    }

    .user_time {
      color: #ccc;
      font-size: 24rpx;
      padding: 10rpx 0;
    }
  }
}

.user_rank {
  display: flex;
  height: 80rpx;
  border-bottom: 5rpx solid #eee;
  .ran {
    flex:1;
    display: flex;
    justify-content: center;
    align-items: center;
    .iconfont {

    }
  }

  .user_collect {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
    .iconfont{

    }
  }
}
.album_wrap {
  padding: 20rpx 0;
  border-bottom: 10rpx solid #eee;

  .album_title {
    padding: 10rpx;
  }

  .album_list {
    padding: 0 10rpx;
    .album_item {
      display: flex;
      padding: 10rpx 0;
      .album_cover {
        flex: 1;
        image {
          width: 180rpx;
          height: 180rpx;
        }
      }

      .album_info {
        flex: 3;
        padding-left: 20rpx;
        position: relative;
        .album_info_text {
          width: 100rpx;
          height: 50rpx;
          background-color: $color;
          color: #fff;
          display: flex;
          justify-content: center;
          align-items: center;
        }

        .album_name {
          padding: 10rpx 0;
          color: #888;
        }
        .iconfont {
          font-size: 40rpx;
          position: absolute;
          top: 50%;
          // css3 变换 转换
          transform: translateY(-50%);
          right: 10%;
          color: #000;
        }
      }
    }
  }
}

.comment_hot {
  .comment_title {
    padding: 15rpx;
    .iconfont {
      color: $color;
      font-size: 36rpx;
    }

    .comment_text {
      font-weight: 600;
      font-style: 28rpx;
      color: #666;
      margin-left: 10rpx;
    }
  }
  
}
.comment_list {

    .comment_item {
      border-bottom: 15rpx solid #eee;
      padding: 20rpx 0;
      .commetn_user {
        display:flex;
        .user_icon {
          width: 15%;
          display: flex;
          justify-content: center;
          align-items: center;
          padding: 0 10rpx;
          img {
            width: 90%;
          }
        }

        .user_name {
          flex: 1;
          .user_nickname {
            color: #777;
          }

          .user_time {
            color: #ccc;
            font-size: 24rpx;
            padding: 5rpx;
          }
        }

        .user_badge {
          img {
            width: 40rpx;
            width: 40rpx;
            display: inline-block;
          }
        }
      }

      .comment_desc {
        display: flex;
        // padding: 10rpx 5rpx 20rpx 0;
        padding-right: 10rpx;
        .comment_content {
          flex:1;
          padding-left: 15%;
          color: #000;
        }

        .comment_like {
          text-align: right;
          .icondianzan {

          }
        }
      }
    }
}

.download{
  height: 120rpx;
  display:flex;
  justify-content: center;
  align-items: center;
  .download_btn{
    width: 90%;
    height: 80%;
    background-color: $color;
    color: #fff;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 50rpx;
    font-weight: 600;
  }
}
</style>