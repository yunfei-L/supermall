<template>
  <div id="detail">
    <detail-nav-bar
      class="detail-nav"
      @titleClick="titleClick"
      ref="nav"
    ></detail-nav-bar>
    <scroll class="content" :probe-type="3" :pull-up-load="true" ref="scroll" @scroll="contentScroll">
      <detail-swiper :top-images="topImages"></detail-swiper>
      <detail-base-info :goods="goods"></detail-base-info>
      <detail-shop-info :shop="shop"></detail-shop-info>
      <detail-goods-info
        :detail-info="detailInfo"
        @imageLoad="imageLoad"
      ></detail-goods-info>
      <detail-param-info
        ref="params"
        :param-info="paramInfo"
      ></detail-param-info>
      <detail-comment-info
        ref="comment"
        :comment-info="commentInfo"
      ></detail-comment-info>
      <good-list ref="recommend" :goods="recommends"></good-list>
    </scroll>
    <detail-bottom-bar @addCart="addToCart"></detail-bottom-bar>
    <back-top @click.native="backClick" v-show="isShowBackTop"></back-top>
  </div>
</template>

<script>
import DetailNavBar from "./childCompos/DetailNavBar.vue";
import DetailSwiper from "./childCompos/DetailSwiper.vue";
import DetailBaseInfo from "./childCompos/DetailBaseInfo.vue";
import DetailShopInfo from "./childCompos/DetailShopInfo.vue";
import DetailParamInfo from "./childCompos/DetailParamInfo.vue";
import DetailCommentInfo from "./childCompos/DetailCommentInfo.vue";

import Scroll from "components/common/scroll/Scroll";
import {
  getDetail,
  Goods,
  Shop,
  GoodsParam,
  getRecommend,
} from "../../network/detail";
import DetailGoodsInfo from "./childCompos/DetailGoodsInfo.vue";
import GoodList from "components/content/goods/GoodList";
import { itemListenerMixin,backTopMixin } from "../../common/mixin";
import DetailBottomBar from './childCompos/DetailBottomBar.vue';


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
    DetailBottomBar,
    
  },
  mixins: [itemListenerMixin,backTopMixin],
  data() {
    return {
      iid: null,
      topImages: [],
      goods: {},
      shop: {},
      detailInfo: {},
      paramInfo: {},
      commentInfo: {},
      recommends: [],
      themeTopYs: [],
      currentIndex:0
    };
  },
  created() {
    // console.log(this.$route.params)
    this.iid = this.$route.params.id;
    //请求详情数据
    getDetail(this.iid).then((res) => {
      const data = res.result;
      //1.获取顶部的图片轮播图数据
      this.topImages = data.itemInfo.topImages;

      //2.获取商品信息
      this.goods = new Goods(
        data.itemInfo,
        data.columns,
        data.shopInfo.services
      );

      //3.创建店铺信息的对象
      this.shop = new Shop(data.shopInfo);

      //4.保存商品的详情数据
      this.detailInfo = data.detailInfo;

      //5.获取参数的信息
      this.paramInfo = new GoodsParam(
        data.itemParams.info,
        data.itemParams.rule
      );

      //6.取出评论信息
      if (data.rate.cRate !== 0) {
        this.commentInfo = data.rate.list[0];
      }

      this.$nextTick(() => {
        //根据最新的数据，对应的DOM是已经被渲染出来
        //但是图片依然是没有加载出来
        //offsetTop值不对的时候，都是因为图片的问题
        
      });
    });
    //获取推荐数据
    getRecommend().then((res) => {
      this.recommends = res.data.list;
    });
  },
  methods: {
    imageLoad() {
      this.$refs.scroll.refresh();
      this.themeTopYs.push(0);
        this.themeTopYs.push(this.$refs.params.$el.offsetTop);
        this.themeTopYs.push(this.$refs.comment.$el.offsetTop);
        this.themeTopYs.push(this.$refs.recommend.$el.offsetTop);
        this.themeTopYs.push(Number.MAX_VALUE)
        // console.log(this.themeTopYs)
    },
    titleClick(index) {
      this.$refs.scroll.scrollTo(0, -this.themeTopYs[index], 100);
    },
    contentScroll(position){

      this.isShowBackTop = -position.y > 1000
      // console.log(position)
      //1.获取y值
      const positionY = -position.y
      // console.log(positionY)

      //2.positionY和主题中值进行比较
      let length = this.themeTopYs.length
      for(let i=0; i< length-1; i++){
        if(this.currentIndex !== i &&(positionY >= this.themeTopYs[i] && positionY < this.themeTopYs[i+1])){
            this.currentIndex = i
            this.$refs.nav.$el.currentIndex = this.currentIndex
        }
      }

    },
    addToCart(){
      //1.获取购物车需要展示的信息
      const product = {}
      product.image = this.topImages[0]
      product.title = this.goods.title
      product.desc = this.goods.desc
      product.price = this.goods.nowPrice
      product.iid = this.iid

      //将商品添加到购物车
      this.$store.dispatch('addCart',product)

      
    }
   
  },
  mounted() {},
  destroyed() {
    this.$bus.$off("itemImgLoad", this.itemImgListener);
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
.detail-nav {
  position: relative;
  z-index: 9;
  background-color: #fff;
}
</style>