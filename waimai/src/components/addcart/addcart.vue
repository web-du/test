<template lang="html">
  <div class="add-box">
    <transition name="move">
      <div class="reduce iconfont icon-remove_circle_outlin" v-on:click='reduce' v-show="foods.num"></div>
    </transition>
    <div class="num" v-show="foods.num>0">
      {{foods.num}}
    </div>
    <div class="add iconfont icon-add_circle" v-on:click='add'>

    </div>
  </div>
</template>

<script>
import Vue from 'vue'
export default {
  props:{
    foods:{
      type:Object
    }
  },
  name:"addcart",
  methods:{
    add:function(){
      if(!this.foods.num){
        Vue.set(this.foods,'num',1);
      }else{
        this.foods.num ++;
        console.log(this.foods.num);
      }
    },
    reduce:function(){
      if(this.foods.num){
        this.foods.num --;
      }
    }
  },
  computed:{
    nownum:function(){
      var num = this.foods.num;
      return num;
    }
  }
  }
</script>

<style lang="css">
  .add-box{
    display:flex;
  }
  .add-box .reduce{
    font-size: 24px;
    color:rgb(0,160,220);
    line-height: 24px;
    padding: 6px;
    transition: all 0.4s;
    transform: translate3d(0,0,0);
  }
  .add-box .reduce.move-enter-active,.add-box .reduce.move-leave-active{
    transition: all 0.4s;
  }
  .add-box .reduce.move-enter,.add-box .reduce.move-leave-active{
    opacity: 0;
    transform: rotate(180deg) translate3d(-24px,0,0);
  }
  .add-box .num{
    font-size: 10px;
    color:rgb(147,153,159);
    line-height: 36px;
  }
  .add-box .add{
    font-size: 24px;
    color:rgb(0,160,220);
    line-height: 24px;
    padding: 6px;
  }
</style>
