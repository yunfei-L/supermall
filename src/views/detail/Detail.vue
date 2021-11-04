<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav"></detail-nav-bar>
    <scroll class="content" :probe-type="3" :pull-up-load="true" ref="scroll">
      <detail-swiper :top-images="topImages"></detail-swiper>
      <detail-base-info :goods="goods"></detail-base-info>
      <detail-shop-info :shop="shop"></detail-shop-info>
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad"></detail-goods-info>
      <detail-param-info :param-info="paramInfo"></detail-param-info>
      <detail-comment-info :comment-info="commentInfo"></detail-comment-info>
      <good-list :goods= "recommends"></good-list>
    </scroll>
  </div>
</template>

<script>
import DetailNavBar from "./childCompos/DetailNavBar.vue";
import DetailSwiper from "./childCompos/DetailSwiper.vue";
import DetailBaseInfo from "./childCompos/DetailBaseInfo.vue";
import DetailShopInfo from "./childCompos/DetailShopInfo.vue";
import DetailParamInfo from "./childCompos/DetailParamInfo.vue";
import DetailCommentInfo from "./childCompos/DetailCommentInfo.vue"


import Scroll from "components/common/scroll/Scroll";
import { getDetail, Goods, Shop,GoodsParam,getRecommend} from "../../network/detail";
import DetailGoodsInfo from './childCompos/DetailGoodsInfo.vue';
import GoodList from 'components/content/goods/GoodList'
import {itemListenerMixin} from '../../common/mixin'

export default {
  name: "Detail",
  components: {
    DetailNavBar,
    DetailSwiper,
    DetailBaseInfo,
    DetailShopInfo,
    Scroll,
    DetailGoodsInfo,
    DetailParamInfo,
    DetailCommentInfo,
    GoodList,
  },
  mixins:[itemListenerMixin],
  data() {
    return{
      iid: null,
      topImages: [],
      goods: {},
      shop: {},
      detailInfo:{},
      paramInfo:{},
      commentInfo:{},
      recommends:[]
    };
  },
  created() {
    // console.log(this.$route.params)
    this.iid = this.$route.params.id;
    //请求详情数据
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

      //5.获取参数的信息
      this.paramInfo = new GoodsParam(data.itemParams.info,data.itemParams.rule)

      //6.取出评论信息
      if(data.rate.cRate !== 0){
        this.commentInfo = data.rate.list[0]
      }
    });
    //获取推荐数据
    getRecommend().then(res => {
      this.recommends = res.data.list
    })
  },
  methods:{
      imageLoad(){
          this.$refs.scroll.refresh()
      }
  },
  mounted(){
    // console.log(2)
  },
  destroyed(){
    this.$bus.$off('itemImgLoad',this.itemImgListener)
  }
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