<template>
<div class="header">
    <div class="content-wrap">
        <div class="avatar">
          <img :src="msg.avatar" />
        </div>
        <div class="content">
          <div class="title">
            <span class="brand"><img src="./brand@2x.png" /></span>
            <span class="name">{{ msg.name }}</span>
          </div>
          <div class="description">
            {{ msg.description }} / {{msg.deliveryTime}}分钟送达
          </div>
          <div class="support">
            <span class="icon" :class="classArr[msg.supports[0].type]"></span>
            <span class="text">{{ msg.supports[0].description }}</span>
          </div>
          <div class="support-num" @click="show()">
            <span class="num">{{msg.supports.length}}个<i class="icon-right">&gt;</i></span>
          </div>
        </div>
    </div>
    <div class="notic-wrap" @click="show()">
      <img src="./bulletin@2x.png" />
      <p class="bulletin">{{ msg.bulletin }}</p>
      <span>&gt;</span>
    </div>
    <div class="header-background">
      <img :src="msg.avatar" width="100%" height="100%"/>
    </div>
    <transition name="fade">
      <div class="detail" v-show="showDetail">
        <div class="detail-wrap clearfix">
          <div class="detail-content">
            <h1>{{ msg.name }}</h1>
            <div class="star-con">
              <star :size="48" :score="msg.score"></star>
            </div>
            <div class="detail-title">
              <div class="line"></div>
              <div class="text">
                优惠信息
              </div>
              <div class="line"></div>
            </div>
            <ul class="supports">
              <li v-for="item in msg.supports">
                <span class="icon" :class="classArr[item.type]"></span>
                <span class="text">{{ item.description }}</span>
              </li>
            </ul>
            <div class="detail-title">
              <div class="line"></div>
              <div class="text">
                商家公告
              </div>
              <div class="line"></div>
            </div>
            <div class="detail-bulletin">
              <p>{{msg.bulletin}}</p>
            </div>
          </div>
        </div>
        <div class="detail-close" @click="hide">
          ╳
        </div>
    </div>
    </transition>
</div>
</template>

<script>
import star from "@/components/star/star"
    export default{
        name:"header",
        data(){
          return {
            msg:{},
            showDetail:false
          }
        },
        // 加载之前先取得数据
        created:function(){
          var _this = this
          this.$http.get('/api/seller').then(function(respone){
            if(respone.data.err == 0){
              _this.msg = respone.data.data;
            }
          })
          this.classArr = ["decrease","discount","special","invoice","guarantee"]
        },
        components:{
          star:star
        },
        methods:{
          show:function(){
            this.showDetail = true;
          },
          hide:function(){
            this.showDetail = false;
          }
        }
    }
</script>

<style>
    .header{
        background-color: rgba(0,0,0,0.5);
        position:relative;
    }
    .content-wrap{
      display: flex;
      padding: 24px 12px 18px 24px;
      position: relative;
    }
    .avatar{
      flex:0 0 64px;
      margin-right: 16px;
    }
    .avatar img{
      width: 64px;
      height: 64px;
      border-radius: 2px;
    }
    .content-wrap .content{
      flex:1;
      padding: 2px 0;
    }
    .content .title{
      height: 18px;
      margin-bottom: 8px;
    }
    .content .brand{
      float: left;
      margin-right: 6px;
    }
    .brand img{
      width: auto;
      height: 18px;
    }
    .content .name{
      font-size: 16px;
      line-height: 18px;
      color:white;
      font-weight:bold;
    }
    .description{
      font-size: 12px;
      line-height:1;
      color:#fff;
      font-weight: 200;
      margin-bottom: 10px;
    }
    .support{
      height: 12px;
    }
    .support .icon{
      float: left;
      width: 12px;
      height: 12px;
      margin-right: 6px;
      background:url('decrease_1@2x.png') 0 0 no-repeat;
      background-size: 12px 12px;
    }
    .support .decrease{
      background-image: url("decrease_1@2x.png");
    }
    .support .discount{
      background-image: url("discount_1@2x.png");
    }
    .support .guarantee{
      background-image: url("guarantee_1@2x.png");
    }
    .support .invoice{
      background-image: url("invoice_1@2x.png");
    }
    .support .special{
      background-image: url("special_1@2x.png");
    }
    .support .text{
      font-size: 10px;
      color:#fff;
      font-weight: 200;
      line-height: 12px;
      float:left;
    }
    .support-num{
      position: absolute;
      right:12px;
      bottom: 18px;
      line-height: 24px;
      height: 24px;
      padding: 0 8px;
      border-radius: 14px;
      background-color: rgba(0,0,0,0.2);
      cursor: pointer;
    }
    .support-num .num{
      font-size: 10px;
      color:#fff;
    }
    .support-num .icon-right{
      margin-left: 2px;
      line-height: 24px;
      font-size: 10px;
      font-style:normal;
      color: #fff;
    }
    .notic-wrap{
      display: flex;
      align-items: center;
      padding: 0 12px;
      height: 28px;
    }
    .notic-wrap img{
      height: 20px;
      width: auto;
    }
    .notic-wrap .bulletin{
      font-size: 10px;
      color:rgb(255,255,255);
      font-weight: 200;
      line-height: 28px;
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
      margin: 0 4px;
    }
    .notic-wrap span{
      color:white;
    }
    .header-background{
      position:absolute;
      top:0;
      left:0;
      width: 100%;
      height: 100%;
      z-index: -1;
      filter: blur(10px);
    }
    .clearfix{
      display: inline-block;
    }
    .clearfix:after{
      content: ".";
      clear:both;
      display:block;
      height: 0;
      visibility: hidden;
    }
    .detail{
      position: fixed;
      background-color: rgba(7,17,27,0.9);
      width: 100%;
      height: 100%;
      top:0;
      left:0;
      z-index: 100;
    }
    .detail-wrap{
      width: 100%;
      min-height: 100%;
    }
    .detail-content{
      margin-top: 64px;
      padding-bottom: 64px;
    }
    .detail-close{
      position:relative;
      width: 32px;
      height: 32px;
      margin:-64px auto 0;
      clear: both;
      font-size: 32px;
      color:rgba(255,255,255,0.5);
    }
    .detail-content h1{
      font-size: 16px;
      color:white;
      font-weight: 700;
      text-align: center;
    }
    .star-con{
      margin:16px auto 18px;
      text-align: center;
    }
    .detail-title{
      display: flex;
      margin: 28px auto 24px;
      padding: 0 36px;
    }
    .detail-title .line{
      flex:1;
      height: 1px;
      background-color: rgba(255,255,255,0.2);
      margin-top: 6px;
    }
    .detail-title .text{
      font-size: 14px;
      color:white;
      font-weight: 700;
      flex:0 0 auto;
      margin: 0 12px;
    }
    .supports{
      padding: 0 36px;
    }
    .supports li{
      height: 16px;
      margin-bottom: 12px;
      padding-left: 12px;
    }
    .supports .icon{
      float: left;
      width: 16px;
      height: 16px;
      margin-right: 12px;
      background:url('decrease_1@2x.png') 0 0 no-repeat;
      background-size: 16px 16px;
    }
    .supports .decrease{
      background-image: url("decrease_1@2x.png");
    }
    .supports .discount{
      background-image: url("discount_1@2x.png");
    }
    .supports .guarantee{
      background-image: url("guarantee_1@2x.png");
    }
    .supports .invoice{
      background-image: url("invoice_1@2x.png");
    }
    .supports .special{
      background-image: url("special_1@2x.png");
    }
    .supports .text{
      font-size: 12px;
      color:#fff;
      font-weight: 200;
      line-height: 16px;
      float:left;
    }
    .detail-bulletin{
      padding: 0 48px;
      font-size: 12px;
      color:white;
      line-height: 24px;
    }
    .fade-enter-active,.fade-leave-active{
      transition:opacity .5s;
    }
    .fade-enter,.fade-leave-to{
      opacity: 0;
    }
</style>
