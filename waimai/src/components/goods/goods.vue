<template>
  <div class="goods">
    <div class="menu" ref="menuScroll">
      <ul>
        <li v-for="(item,index) in goods" :class="{'on':index == onIndex}" ref="menuList" v-on:click="scrollTo(index)">
          <span class="text"><span class="icon" v-if="item.type>=0" :class="classArr[item.type]"></span>{{item.name}}</span>
        </li>
      </ul>
    </div>
    <div class="list"ref="listScroll">
      <ul class="foodItem">
        <li v-for="item in goods" ref="foodsList">
          <h3 class="title">{{item.name}}</h3>
          <ul class="foodList">
            <li v-for="(item1,index) in item.foods">
              <div class="icon">
                <img :src="item1.icon" v-on:click="goodsdatail(item1)"/>
              </div>
              <div class="content">
                <h2 class="name">{{item1.name}}</h2>
                <div class="desc">
                  {{item1.description}}
                </div>
                <div class="sellCount">
                  <span>月售{{item1.sellCount}}份</span>
                  <span>好评率{{item1.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now"><i>￥</i>{{item1.price}}</span>
                  <span class="oldPrice" v-show="item1.oldPrice"><i>￥</i>{{item1.oldPrice}}</span>
                </div>
              </div>
              <div class="list-add">
                <addcart :foods="item1"></addcart>
              </div>

            </li>
          </ul>
        </li>

      </ul>
    </div>
    <shopcart :cartArr = "foodsTocart" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice"></shopcart>
    <div class="goodsBox" ref="goodSlide" v-show="flag">
      <div class="goodsdetails">
        <div class="goodsimage">
          <img :src="nowIndex.image" />
        </div>
        <div class="goodsName">
          <h2>{{nowIndex.name}}</h2>
          <p>月售{{nowIndex.sellCount}}份<span>好评率{{nowIndex.rating}}%</span></p>
          <div class="goodsPrice">
            <span><i>￥</i>{{nowIndex.price}}<del v-show="nowIndex.oldPrice"><i>￥</i>{{nowIndex.oldPrice}}</del></span>
            <span v-on:click="tianjia" v-show="!nowIndex.num">加入购物车</span>
            <addcart :foods="nowIndex" v-show="nowIndex.num"></addcart>
          </div>
        </div>
        <div class="line"></div>
        <div class="goodintro" v-show="nowIndex.info">
          <h2>商品介绍</h2>
          <div class="goodtext">
            {{nowIndex.info}}
          </div>
        </div>
        <div class="line" v-show="nowIndex.info"></div>
        <div class="goodscomment">
          <h2>商品评价</h2>
          <div class="allcom">
            <span v-on:click="showAll">全部<i>{{goodsAll}}</i></span>
            <span v-on:click="showGood">推荐<i>{{goodsgood}}</i></span>
            <span v-on:click="showBad">吐槽<i>{{goodsbad}}</i></span>
          </div>
        </div>
        <div class="havetext">
          <span class="iconfont icon-check_circle" :class="see" v-on:click="onlyhavetext"></span>
          <p>只看有内容的评论</p>
        </div>
        <div class="goodscom">
          <ul>
            <li v-for="com in onlyselect">
              <div class="liBox">
                <div class="comtime">
                  <p>{{com.rateTime | nowTime }}</p>
                  <p>{{com.username}}<img :src="com.avatar"></p>
                </div>
                <div class="comtext">
                  <p><span class="iconfont icon-thumb_up"></span>{{com.text}}</p>
                </div>
              </div>
            </li>
          </ul>
        </div>
      </div>
      <div class="goback" v-on:click="Goback">
        返回
      </div>
    </div>
  </div>
</template>


<script type="text/javascript">
import Vue from 'vue'
import shopcart from '@/components/shopcart/shopcart'
import addcart from '@/components/addcart/addcart'
import bs from 'better-scroll';
  export default{
    name:"goods",
    props:{
      seller:{
        type:Object
      }
    },
    data(){
      return {
        goods:[],
        listHeight:[],
        scrollHeight:0,
        nowIndex:{},
        flag:false,
        goodsAll:0,
        goodsgood:0,
        goodsbad:0,
        selectcom:[],
        onlyselect:[],
        ready:false
      }
    },
    created:function(){
      var _this = this
      this.$http.get('/api/goods').then(function(respone){
        if(respone.data.err == 0){
          _this.goods = respone.data.data;
          _this.$nextTick(function(){
            _this.initScroll();
            _this.initScroll1();
            _this.getHeight();

          })
        }
      })
      this.classArr = ["decrease","discount","special","invoice","guarantee"]
    },
    computed:{
      onIndex:function(){
        for(let i = 0;i<this.listHeight.length;i++){
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i+1];
          if(this.scrollHeight>=height1 && this.scrollHeight<height2){
            this.menuScroll.scrollToElement(this.$refs.menuList[i],300,0,0);
            return i;
          }
        }
      },
      foodsTocart(){
        let cartArr = [];
        for(let i = 0;i<this.goods.length;i++){
          for(let j = 0;j<this.goods[i].foods.length;j++){
            if(this.goods[i].foods[j].num>0){
              cartArr.push(this.goods[i].foods[j]);
            }
          }
        }
        return cartArr;
      },
      see(){
        let kan = '';
        if(this.ready){
          kan = "chu";
        }else{
          kan = '';
        }
        return kan;
      }
    },
    methods:{
      initScroll:function(){
        this.menuScroll = new bs(this.$refs.menuScroll,{
          click:true
        })
        this.listScroll = new bs(this.$refs.listScroll,{
          click:true,
          probeType:3
        })
        this.listScroll.on('scroll',(pos) => {
          this.scrollHeight = (Math.abs(Math.round(pos.y)));
        })
      },
      initScroll1(){
        this.goodSlide = new bs(this.$refs.goodSlide,{
          click:true,
          bounce:false
        })
      },
      getHeight:function(){
        let height = 0;
        this.listHeight.push(height);
        for(var i = 0;i<this.$refs.foodsList.length;i++){
          height += this.$refs.foodsList[i].clientHeight;
          this.listHeight.push(height);
        }
      },
      scrollTo:function(index){
        this.listScroll.scrollToElement(this.$refs.foodsList[index],300,0,0);
      },
      goodsdatail:function(item1){
        this.selectcom=[];
        let good=0;
        let bad = 0;
        this.nowIndex = item1;
        this.flag = true;
        this.goodsAll = this.nowIndex.ratings.length;
        for(var i = 0;i<this.nowIndex.ratings.length;i++){
          this.selectcom.push(this.nowIndex.ratings[i])
          this.onlyselect.push(this.nowIndex.ratings[i])
          if(this.nowIndex.ratings[i].rateType == 0){
            good++
          }
          if(this.nowIndex.ratings[i].rateType == 1){
            bad++
          }
        }
        this.goodsgood = good;
        this.goodsbad = bad;
      },
      Goback(){
        this.flag = false;
      },
      tianjia:function(){
        if(!this.nowIndex.num){
          Vue.set(this.nowIndex,'num',1);
        }else{
          this.nowIndex.num ++;
        }
      },
      showAll:function(){
        this.onlyselect=[];
        for(var i = 0;i<this.nowIndex.ratings.length;i++){
          this.onlyselect.push(this.nowIndex.ratings[i])
        }
        this.selectcom = this.onlyselect;
    },
    showGood:function(){
      this.onlyselect=[];
      for(var i = 0;i<this.nowIndex.ratings.length;i++){
        if(this.nowIndex.ratings[i].rateType == 0){
          this.onlyselect.push(this.nowIndex.ratings[i])
        }
      }
      this.selectcom = this.onlyselect;
    },
    showBad:function(){
      this.onlyselect=[];
      for(var i = 0;i<this.nowIndex.ratings.length;i++){
        if(this.nowIndex.ratings[i].rateType == 1){
          this.onlyselect.push(this.nowIndex.ratings[i])
        }
      }
      this.selectcom = this.onlyselect;
    },
    onlyhavetext:function(){
      this.ready = !this.ready
      if(this.ready){
        this.onlyselect=[];
        for(var i = 0;i<this.selectcom.length;i++){
          if(this.selectcom[i].text){
            this.onlyselect.push(this.selectcom[i])
          }
        }
        // this.selectcom = this.onlyselect;
      }else{
        // this.onlyselect = this.selectcom;
        // this.selectcom = [];
        // for(var i = 0;i<this.nowIndex.ratings.length;i++){
        //   this.selectcom.push(this.nowIndex.ratings[i]);
        // }
        this.onlyselect=[];
        for(var i = 0;i<this.selectcom.length;i++){
            this.onlyselect.push(this.selectcom[i])
        }
        // this.selectcom = this.onlyselect;
      }

    }
  },
    components:{
      'addcart':addcart,
      'shopcart':shopcart
    },
    filters:{
      nowTime:function(value){
        // value = value/1000;
        var time = new Date(value);
        var y = time.getFullYear();
        var m = (time.getMonth()+1)<10?'0'+(time.getMonth()+1):(time.getMonth()+1);
        var d = time.getDate()<10?'0'+time.getDate():time.getDate();
        var h = time.getHours()<10?'0'+time.getHours():time.getHours();
        var mm = time.getMinutes()<10?'0'+time.getMinutes():time.getMinutes();
        var s = time.getSeconds()<10?'0'+time.getSeconds():time.getSeconds();
        return y+'-'+m+'-'+d+' '+h+':'+mm+':'+s;
      }
    },
  }
</script>

<style>
.goodsBox .goback{
  position:fixed;
  width: 40px;
  height: 20px;
  top:0;
  left:0;
  z-index:1000;
  color: white;
  background-color: black;
}
.goodsBox{
  position: absolute;
  top:-177px;
  bottom:0;
  width:100%;
  overflow: hidden;
  z-index: 888;
}
.goods{
  display: flex;
  position: absolute;
  bottom: 48px;
  top:175px;
  overflow: hidden;
  width: 100%;
}
.menu{
  flex:0 0 80px;
  width: 80px;
  background-color: #f3f5f7;
}
.menu li{
  height: 54px;
  padding: 0 12px;
  display: table;
}
.menu li.on{
  background-color: red;
}
.menu li.on .text{
  color: white;
}
.menu span.text{
  display: table-cell;
  vertical-align: middle;
  font-size: 12px;
  width: 56px;
  margin: 0 auto;
  color:rgb(77,85,93);
  line-height: 14px;
  border-bottom: 1px solid rgba(7,17,27,0.1);
}
.menu .icon{
  display: inline-block;
  width: 12px;
  height: 12px;
  margin-right: 6px;
  background:url('decrease_1@2x.png') 0 0;
  vertical-align: top;
  background-size: 12px 12px;
}
.menu .decrease{
  background-image: url("decrease_3@2x.png");
}
.menu .discount{
  background-image: url("discount_3@2x.png");
}
.menu .guarantee{
  background-image: url("guarantee_3@2x.png");
}
.menu .invoice{
  background-image: url("invoice_3@2x.png");
}
.menu .special{
  background-image: url("special_3@2x.png");
}
.list{
  flex:1;
}
.foodItem .title{
  background-color: #f3f5f7;
  height: 26px;
  padding-left: 14px;
  font-size: 12px;
  color:rgb(147,153,159);
  line-height: 26px;
  font-weight: 200;
  border-left: 2px solid #d9dde1;
}
.foodList li{
  padding: 18px 0;
  margin: 0 18px;
  display: flex;
  border-bottom: 1px solid rgba(7,17,27,0.1);
  position: relative;
}
.foodList li:last-child{
  border-bottom:0;
}
.goodsdetails{
  position:fixed;
  top:0;
  left:0;
  width: 100%;
  height: auto;
  background-color: white;
}
.foodList .icon{
  flex:0 0 56px;
  margin-right: 10px;
}
.foodList .icon img{
  width: 56px;
  height: 56px;
  border-radius: 2px;
}
.foodList .name{
  font-size: 14px;
  color:rgb(7,17,27);
  line-height: 14px;
  font-weight: 200;
  margin-top: 2px;
}
.foodList .desc{
  font-size: 10px;
  color:rgb(147,153,159);
  line-height: 10px;
  margin-top:8px;
}
.foodList .sellCount{
  font-size: 10px;
  color:rgb(147,153,159);
  line-height: 10px;
  margin-top:8px;
}
.sellCount span{
  margin-right: 12px;
}
.foodList .price{
  margin-top: 8px;
}
.foodList .now{
  font-size: 14px;
  line-height: 24px;
  color:rgb(240,20,20);
  font-weight: 700;
}
.foodList .now i{
  font-style: normal;
  font-size: 10px;
  font-weight: normal;
  margin-right: 8px;
}
.foodList .oldPrice{
  font-size: 10px;
  line-height: 24px;
  color:rgb(147,153,159);
  font-weight: 700;
  text-decoration: line-through;
}
.foodList .oldPrice i{
  font-style: normal;
  font-size: 10px;
  font-weight: normal;
}
.foodList li .list-add{
  position: absolute;
  right:0px;
  bottom: 18px;
}
.goodsimage{
  width: 100%;
  height: 375px;
}
.goodsimage img{
  width: 100%;
  height: 100%;
}
.goodsName{
  padding: 18px;
}
.goodsName h2{
  font-size: 14px;
  font-weight: 700;
  color:rgb(7,17,27);
  line-height: 14px;
  margin-bottom: 8px;
}
.goodsName p{
  font-size: 10px;
  color:rgb(147,153,159);
  line-height: 10px;
  margin-bottom: 18px;
}
.goodsName p span{
  margin-left: 12px;
}
.goodsName .goodsPrice{
  display: flex;
  justify-content: space-between;
}
.goodsName .goodsPrice span:nth-child(1){
  font-size: 14px;
  font-weight: 700;
  color:rgb(240,20,20);
  line-height: 24px;
}
.goodsName .goodsPrice span i{
  font-style: normal;
  font-size: 10px;
  font-weight: normal;
}
.goodsName .goodsPrice span del{
  font-size: 10px;
  font-weight: 700;
  color:rgb(147,153,159);
  line-height: 24px;
  margin-left: 6px;
}
.goodsName .goodsPrice span del i{
  font-weight: normal;
}
.goodsName .goodsPrice span:nth-child(2){
  font-size: 10px;
  color:rgb(255,255,255);
  line-height: 12px;
  background-color: rgb(0,160,220);
  border-radius: 12px;
  padding: 6px 12px;
}
.line{
  width: 100%;
  height: 16px;
  box-sizing: border-box;
  background-color: #f3f5f7;
  border-top: 1px solid rgba(7,17,27,0.1);
  border-bottom: 1px solid rgba(7,17,27,0.1);
}
.goodintro{
  padding: 18px;
}
.goodintro h2{
  font-size: 14px;
  font-weight: 700;
  margin-bottom: 6px;
}
.goodintro .goodtext{
  width: 100%;
  box-sizing: border-box;
  padding:0 8px;
  font-size: 12px;
  font-weight: 200;
  color:rgb(77,85,93);
  line-height: 24px;
}
.goodscomment{
  padding: 18px 18px 0 18px;
}
.goodscomment h2{
  font-size: 14px;
  font-weight: 700;
}
.goodscomment .allcom{
  display: flex;
  padding: 18px 0;
  box-sizing: border-box;
  border-bottom: 1px solid rgba(7,17,27,0.1);
}
.goodscomment .allcom span{
  margin-right: 8px;
  padding: 8px 12px;
  font-size: 12px;
  color:rgb(255,255,255);
  line-height: 16px;
  text-align: center;
}
.goodscomment .allcom span i{
  font-size: 8px;
  font-style: normal;
  margin-left: 4px;
}
.goodscomment .allcom span:nth-child(1){
  background-color: rgb(0,160,220);
}
.goodscomment .allcom span:nth-child(2){
  background-color: rgb(0,160,220,0.2);
  color:rgb(77,85,93);
}
.goodscomment .allcom span:nth-child(3){
  background-color: rgba(77,85,93,0.2);
}
.havetext{
  display: flex;
  box-sizing: border-box;
  width: 100%;
  padding: 12px 18px;
  border-bottom: 1px solid rgba(7,17,27,0.1);
}
.havetext span{
  font-size: 24px;
  color:rgb(147,153,159);
  line-height:24px;
  margin-right: 4px;
}
.havetext span.chu{
  color: rgb(0,160,220);
}
.havetext p{
  font-size: 12px;
  color:rgb(147,153,159);
  line-height: 24px;
}
.goodscom ul li{
  width: 100%;
  box-sizing: border-box;
  padding: 0 18px;
}
.goodscom ul li .liBox{
  box-sizing: border-box;
  padding: 16px 0;
  border-bottom: 1px solid rgba(7,17,27,0.1);
}
.goodscom ul li .comtime{
  display: flex;
  justify-content: space-between;
}
.goodscom ul li .comtime p{
  font-size: 10px;
  color:rgb(147,153,159);
  line-height: 12px;
}
.goodscom ul li .comtime img{
  width: 12px;
  height: 12px;
  margin-left: 6px;
  border-radius: 50%;
}
.goodscom ul li .comtext{
  margin-top: 6px;
}
.goodscom ul li .comtext p{
  font-size: 12px;
  color:rgb(7,17,27);
  line-height: 16px;
}
.goodscom ul li .comtext p span{
  font-size: 12px;
  color:rgb(147,153,159);
  line-height: 24px;
  margin-right: 4px;
}
</style>
