<template>
<div class="lunboBox">
<swiper :options="swiperOption" ref="mySwiperA" class="lunbo1">
  <!-- 幻灯内容 -->
  <swiper-slide v-for="(item,index) in images" :key="index" class="lunbo"><img :src="item" /></swiper-slide>
</swiper>
</div>
</template>
<script>
  export default{
    name:'lunbo',
    props:{
      images:{
        type:Array
      }
    },
    data(){
      return {
        swiperOption: {
          // 所有配置均为可选（同Swiper配置）
          direction:'horizontal',
          slidesPerView:2,
          slidesPerGroup:1,
          spaceBetween:20,
          loop:true,
          onTransitionStart(swiper){
           console.log(swiper)
          }
      }
    }
  },
  computed: {
    swiper(){
     return this.$refs.mySwiperA.swiper
    },
    mounted(){
    console.log("每次切换都会触发我");
    this.swiper.slideTo(3, 1000, false)
   }
 }
}
</script>
<style>

</style>
