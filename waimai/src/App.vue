<template>
  <div id="app">
    <vheader></vheader>
    <div class="tab">
      <ul>
        <li><router-link to="/goods">商品</router-link></li>
        <li><router-link to="/comment">评价</router-link></li>
        <li><router-link to="/seller">商家</router-link></li>
      </ul>
    </div>
    <router-view :seller="seller"></router-view>
  </div>
</template>

<script>
import header from '@/components/header/header.vue'
export default {
  name: 'App',
  data(){
    return {
      seller:{}
    }
  },
  // 注册组件
  components:{
    "vheader":header
  },
  created(){
    var _this = this
    this.$http.get('/api/seller').then(function(respone){
      if(respone.data.err == 0){
        _this.seller = respone.data.data;
      }
  })
}
}
</script>

<style>
  .tab{
    height:40px;
    width:100%;
    border-bottom:1px solid rgba(7,17,27,0.1);
    }
  .tab ul{
    height:40px;
    width:100%;
    display:flex;
  }
  .tab ul li{
  flex:1;
  line-height:40px;
  text-align:center;
  font-size:14px;
  }
  .tab a{
    display: block;
    color:rgb(77,85,93);
  }
  .tab a.router-link-active{
    color:rgb(240,20,20);
  }
</style>
