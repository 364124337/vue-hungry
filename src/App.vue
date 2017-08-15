<template>
  <div id="app">
   <v-header :seller="seller"></v-header>
   <div class="tab border-1px">
     <div class="tab-item">
       <router-link to="/goods">商品</router-link>
     </div>
     <div class="tab-item">
       <router-link to="/ratings">评价</router-link>
     </div>
     <div class="tab-item">
       <router-link to="/seller">商家</router-link>
     </div>
   </div>
   <div class="content">
      <router-view :seller="seller"></router-view>
   </div>
  </div>
</template>

<script>

import vHeader from '@/components/header/header.vue';

const ERR_OK = 0;

export default {
  name: 'app',
  components:{
    vHeader,
  },
  data(){
    return{
      seller:{}
    }
  },
  created(){
    const self = this;
    this.$axios.get('/api/seller').then(function(response){
      if(response.data.errno === ERR_OK){
        self.seller = response.data.seller;
      }
    })
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import 'common/stylus/mixin.styl'

  #app
    .tab
      display:flex
      width:100%
      height:40px
      line-height:40px
      border-1px(rgba(7,17,27,0.1))
      .tab-item
        flex:1
        text-align:center
        & > a
          display:block
          color:rgb(77,85,93)
          font-size:14px
          &.router-link-active
            color:rgb(240,20,20)
          
</style>
