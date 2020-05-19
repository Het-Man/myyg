<template>
    <view class='category_home'>
        <navigator class='cate_item'
            v-for="(item, index) in categoryList" :key="index"
            :url="`/pages/imgCategory/index?id=${item.id}`"
        >
            <image mode='aspectFill' :src='item.cover' ></image>
            <view class="cate_name">{{item.name}}</view>
        </navigator>
    </view>
</template>
<script>
export default {
    data(){
        return {
            categoryList:[],
        }
    },
    mounted() {
        uni.setNavigationBarTitle({title:'分类'})
        this.getList()
    },
    methods:{
        getList(){
            this.request({
                url:'http://157.122.54.189:9088/image/v1/vertical/category'
            })
            .then(result =>{
                console.log(result)
                this.categoryList = result.res.category;
            })
        }
    }
}
</script>

<style lang="scss" >
.category_home{
        display:flex;
        flex-wrap: wrap;
    .cate_item{
        width: 33.33%;
        position: relative;
        border: 5rpx solid #fff;
        image{
            height: 240rpx;
        }
        .cate_name{
            position:absolute;
            bottom:0;
            left:0;
            width: 100%;
            height: 50rpx;
            color:#fff;
            display: flex;
            align-items: center;
            padding-left: 20rpx;
            background-image: linear-gradient(to right top,rgba(0,0,0,.2),rgba(0,0,0,0));
            font-size: 40rpx;
        }
    }
}
</style>