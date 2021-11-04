<template>
  <div class="feature">
      <a href="http://act.mogujie.com/zzlx67">
          <img src="~assets/img/home/recommend_bg.jpg" alt="" @load="FeatureImgLoad">
      </a>
  </div>
</template>

<script>
export default {
    name:"FeatureView",
    methods:{
        FeatureImgLoad(){
            this.$emit('FImgLoad')
        }
    }
}

</script>

<style>
.feature img {
    width: 100%;
}
</style>