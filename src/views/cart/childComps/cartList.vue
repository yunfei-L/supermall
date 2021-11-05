<template>
    <div class="cart-list">
        <scroll class="content">
             <!-- <li v-for="(item,index) in cartList" :key="index">{{item}}</li> -->
             <ul>
                 
             </ul>
        </scroll>
       
    </div>
</template>

<script>
import Scroll from 'components/common/scroll/Scroll'
import {mapGetters} from 'vuex'

export default {
    name:"CartList",
    components:{
        Scroll
        
    },
    computed:{
        ...mapGetters(['cartList'])
    }
}
</script>

<style scoped>
    .cart-list{
        height: calc(100% - 44px - 49px);
    }

    .content{
        height: 100%;
        overflow: hidden;
    }
</style>