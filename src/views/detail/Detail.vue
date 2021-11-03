<template>
    <div id="detail">
        <detail-nav-bar></detail-nav-bar>
        <detail-swiper :top-images="topImages"></detail-swiper>
        <detail-base-info :goods="goods"></detail-base-info>
    </div>
</template>

<script>
import DetailNavBar from './childCompos/DetailNavBar.vue'
import DetailSwiper from './childCompos/DetailSwiper.vue'
import DetailBaseInfo from './childCompos/DetailBaseInfo.vue'

import {getDetail, Goods} from '../../network/detail'


export default {
    name:'Detail',
    components:{
        DetailNavBar,
        DetailSwiper,
        DetailBaseInfo
    },
    data(){
        return {
            iid:null,
            topImages:[],
            goods:{}
        }
    },
    created(){
        // console.log(this.$route.params)
        this.iid = this.$route.params.id

        getDetail(this.iid).then(res => {
            const data = res.result
            //1.获取顶部的图片轮播图数据
            this.topImages = data.itemInfo.topImages

            //2.获取商品信息
            this.goods = new Goods(data.itemInfo,data.columns,data.shopInfo.services)
        })
    }
}
</script>

<style>

</style>