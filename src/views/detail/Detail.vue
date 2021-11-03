<template>
    <div id="detail">
        <detail-nav-bar></detail-nav-bar>
    </div>
</template>

<script>
import DetailNavBar from './childCompos/DetailNavBar.vue'
export default {
    name:'Detail',
    components:{
        DetailNavBar
    },
    data(){
        return {
            iid:null
        }
    },
    created(){
        // console.log(this.$route.params)
        this.iid = this.$route.params.id
    }
}
</script>

<style>

</style>