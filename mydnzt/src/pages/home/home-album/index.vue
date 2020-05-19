<template>
    <scroll-view scroll-y class='album_view'  @scrolltolower='handleToLower' >
        <!-- 轮播图 -->
        <view class="album_swiper">
            <swiper
            autoplay
            indicator-dots
            circular
            >
                <swiper-item
                    v-for="(item, index) in banner" :key="index"
                >
                    <image  :src='item.thumb' ></image>
                </swiper-item>
            </swiper>
        </view>
        <!-- 列表 -->
        <view class="album_list">
            <navigator
                class='album_item'
                v-for="(item, index) in album" :key="index"
                :url="`/pages/album/index?id=${item.id}`"         
            >
                <view class="album_img">
                    <image mode="aspectFill" :src='item.cover'></image>
                </view>
                <view class="album_info">
                    <view class="album_name">{{item.name}}</view>
                    <view class="album_desc">{{item.desc}}</view>
                    <view class="album_btn">
                        <view class="alum_attention">关注</view>
                    </view>
                </view>
            </navigator>
        </view>
    </scroll-view>
</template>
<script>
export default {
    data(){
        return {
            params:{
                limit: 30,
                order: "new",
                skip: 0
            },
            banner:[],
            album:[],
            hasMore:true,
        }
    },
    mounted() {
        uni.setNavigationBarTitle({title:'专辑'})
        this.getZjList()
    },
    methods: {
        getZjList(){
            this.request({
                url:'http://157.122.54.189:9088/image/v1/wallpaper/album',
                data:this.params
            })
            .then(result=>{
                console.log(result)
                if(this.banner.length === 0){
                    this.banner = result.res.banner;
                }

                if(result.res.album.length === 0){
                    this.hasMore = false
                    uni.showToast({
                        title:'没有更多数据了',
                        icon:'none'
                    })
                }
                this.album = [...this.album,...result.res.album];
            })
        },
        handleToLower(){
            // console.log(1)
            if(this.hasMore){
                this.params.skip += this.params.limit
                this.getZjList()
            }else{
                uni.showToast({
                    title: "没有数据了",
                    icon: "none"
                });
            }
        }
    },
}
</script>

<style lang="scss">
    .album_view{
        height:calc( 100vh - 36px);
    }
    .album_swiper{
        swiper{
            // 750rpx 326.0869565217392
            // height: calc(750rpx / 2.3 );
            height: 326.1rpx;
            image {
                height: 100%;
            }

        }
    }
    .album_list {
        padding: 10rpx 0;
        .album_item {
            display: flex;
            padding: 10rpx 0;
            border-bottom: 1rpx solid #ccc;
            .album_img {
                flex:1;
                padding: 10rpx;
                image {
                    width: 200rpx;
                    height: 200rpx;
                }
            }

            .album_info {
                flex:2;
                padding: 0 10rpx;
                overflow: hidden;
                .album_name {
                    font-size: 30rpx;
                    color: #000;
                    padding: 10rpx 0;

                }

                .album_desc {
                    font-size: 24rpx;
                    padding: 10rpx 0;

                    text-overflow: ellipsis;
                    overflow: hidden;
                    white-space: nowrap;
                }

                .album_btn {
                    padding: 10rpx;
                    display: flex;
                    justify-content: flex-end;;
                    .alum_attention {
                        color: $color;
                        border: 1rpx solid $color;
                        padding: 10rpx;
                    }
                }
            }
        }
    }
    
</style>