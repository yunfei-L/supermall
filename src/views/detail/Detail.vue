<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav"></detail-nav-bar>
    <scroll class="content" :probe-type="3" :pull-up-load="true">
      <detail-swiper :top-images="topImages"></detail-swiper>
      <detail-base-info :goods="goods"></detail-base-info>
      <detail-shop-info :shop="shop"></detail-shop-info>
      <detail-goods-info :detail-info="detailInfo"></detail-goods-info>
    </scroll>
  </div>
</template>

<script>
import DetailNavBar from "./childCompos/DetailNavBar.vue";
import DetailSwiper from "./childCompos/DetailSwiper.vue";
import DetailBaseInfo from "./childCompos/DetailBaseInfo.vue";
import DetailShopInfo from "./childCompos/DetailShopInfo.vue";


import Scroll from "components/common/scroll/Scroll";
import { getDetail, Goods, Shop } from "../../network/detail";
import DetailGoodsInfo from './childCompos/DetailGoodsInfo.vue';



export default {
  name: "Detail",
  components: {
    DetailNavBar,
    DetailSwiper,
    DetailBaseInfo,
    DetailShopInfo,
    Scroll,
    DetailGoodsInfo,
  },
  data() {
    return {
      iid: null,
      topImages: [],
      goods: {},
      shop: {},
      detailInfo:{}
    };
  },
  created() {
    // console.log(this.$route.params)
    this.iid = this.$route.params.id;

    getDetail(this.iid).then((res) => {
      const data = res.result
      //1.获取顶部的图片轮播图数据
      this.topImages = data.itemInfo.topImages

      //2.获取商品信息
      this.goods = new Goods(
        data.itemInfo,
        data.columns,
        data.shopInfo.services
      );

      //3.创建店铺信息的对象
      this.shop = new Shop(data.shopInfo)

      //4.保存商品的详情数据
      this.detailInfo = data.detailInfo
    });
  },
};
</script>

<style scoped>
/* #detail {
  position: relative;
  z-index: 10;
  background-color: #fff;
  height: 100vh;
}
.detail-nav{
    position: relative;
    z-index: 10;
    background-color: #fff;
}
.content{
    height: calc(100% -44px);
} */

 #detail {
    height: 100vh;
    position: relative;
    z-index: 1;
    background-color: #fff;
  }

  .content {
    position: absolute;
    top: 44px;
    bottom: 60px;
  }
  .detail-nav{
      position: relative;
      z-index: 9;
      background-color: #fff;
  }
</style>