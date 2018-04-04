<template lang="html">
  <div class="shopcart">
    <div class="content">
      <div class="c-left">
        <div class="cart-logo" v-on:click="showCart">
          <div class="logo-con" :class="{'nozero':totalNum>0}">
            <i class="iconfont icon-shopping_cart"></i>
          </div>
          <div class="num" v-show="totalNum">
            {{totalNum}}
          </div>
        </div>
        <div class="cart-price">
          ￥{{totalPrice}}
        </div>
        <div class="cart-deli">
          另需配送费{{deliveryPrice}}元
        </div>
      </div>
      <div class="c-right">
        <div class="cart-pay" :class="payClass">
          {{qisong}}
        </div>
      </div>
    </div>
    <transition name="fold">
      <div class="cartList" v-show="cartShow">
        <div class="title" v-show = "titleShow">
          <h2>购物车</h2>
          <span class="clean" v-on:click="clean">清空</span>
        </div>
        <div class="ulBox" ref="cartListSlide">
          <ul>
            <li v-for="item in cartArr" ref = "clides">
              <span class="name">{{item.name}}</span>
              <div class="price">
                <i>￥</i>
                <span>{{item.price}}</span>
              </div>
              <div class="goodsCart-up">
                <div class="list-add">
                  <addcart :foods="item"></addcart>
                </div>
              </div>
            </li>
          </ul>
        </div>

    </div>
    </transition>
  </div>
</template>

<script>
import addcart from '@/components/addcart/addcart'
import bs from 'better-scroll';
export default {
  props:{
    cartArr:{
      type:Array
    },
    deliveryPrice:{
      type:Number,
      //默认值
      default:0
    },
    minPrice:{
      type:Number,
      default:0
    }
  },
  data(){
    return {
      cartShow:false,
      allclean:false,
      titleShow:true
    }
  },
  created(){
    this.$nextTick(()=>{
      this.initScroll();
    })
  },
  computed:{
    totalNum(){
      let num=0;
      for(let i = 0;i<this.cartArr.length;i++){
        num+=this.cartArr[i].num;
      }
      return num;
    },
    totalPrice(){
      let price=0;
      for(let i = 0;i<this.cartArr.length;i++){
        price += (this.cartArr[i].num * this.cartArr[i].price);
      }
      return price;
    },
    qisong(){
      if(this.totalPrice == 0){
        return `￥${this.minPrice}元起送`
      }else if(this.totalPrice < this.minPrice){
        let almost = this.minPrice - this.totalPrice;
        return `还差${almost}元起送`
      }else{
        return `去结算`
      }
    },
    payClass(){
      if(this.totalPrice>=this.minPrice){
        return `settlement`
      }else{
        return `no-settlement`
      }
    },
    title:function(){
      return this.foods.num;
    }
  },
  components:{
    'addcart':addcart
  },
  methods:{
    showCart(){
      if(this.totalNum>0){
        this.cartShow = !this.cartShow;
      }
    },
    initScroll(){
      this.cartListSlide = new bs(this.$refs.cartListSlide,{
        click:true
      })
    },
    clean(){
      for(let i = 0;i<this.cartArr.length;i++){
        this.cartArr[i].num=0;
        this.titleShow = false;
      }
    }
  }
}
</script>

<style lang="css">
.shopcart{
  position: fixed;
  height: 48px;
  left:0;
  width: 100%;
  bottom:0;
  z-index: 999;
}
.shopcart .content{
    display: flex;
    height: 48px;
}
.shopcart .content .c-left{
  flex:1;
  height: 48px;
  background-color: #141d27;
}
.shopcart .content .c-left .cart-logo{
  display: inline-block;
  width: 56px;
  height: 56px;
  border-radius: 50%;
  background-color: #141d27;
  position: relative;
  top:-6px;
  left:6px;
  box-sizing: border-box;
  padding: 6px;
}
.shopcart .content .c-left .cart-price{
  display: inline-block;
  color:rgba(255,255,255,0.4);
  font-weight: 700;
  margin-top: 12px;
  line-height: 24px;
  border-right: 1px solid rgba(255,255,255,0.1);
  font-size: 16px;
  padding-right: 12px;
  vertical-align: top;
  padding-left: 18px;
}
.shopcart .content .c-left .cart-deli{
  display: inline-block;
  color:rgba(255,255,255,0.4);
  margin-top: 12px;
  line-height: 24px;
  font-size: 12px;
  margin-right: 12px;
  vertical-align: top;
  padding-left: 12px;

}
.shopcart .content .c-left .cart-logo .logo-con{
  width: 100%;
  height: 100%;
  background-color:#2b343c;
  border-radius: 50%;
  text-align: center;
}
.shopcart .content .c-left .cart-logo .nozero{
  background-color: rgb(0,160,220);
}
.shopcart .content .c-left .cart-logo {
  position: relative;
}
.shopcart .content .c-left .cart-logo .num{
  position: absolute;
  top:0;
  right:0;
  width: 24px;
  height: 16px;
  line-height: 16px;
  text-align: center;
  font-size: 9px;
  color: white;
  border-radius: 12px;
  background-color: rgb(240,20,20);
}
.shopcart .content .c-left .cart-logo .logo-con i{
  font-size: 24px;
  color: rgba(255,255,255,0.4);
  line-height: 44px;
}
.shopcart .content .c-left .cart-logo .nozero i{
  color:white;
}
.shopcart .content .c-right{
  flex:0 0 105px;
  width: 105px;
  height: 48px;
  background-color: #2b333b;
}
.shopcart .content .c-right .cart-pay{
  width: 105px;
  height: 48px;
  text-align: center;
  line-height: 48px;
  font-size: 12px;
  font-weight: 700;
  color: rgba(255,255,255,0.4);
}
.shopcart .content .c-right .settlement{
  background-color: #4cd964;
  color:white;
}
.shopcart .cartList{
  position: absolute;
  left:0;
  bottom: 48px;
  width: 100%;
  z-index: -1;
  transform: translate3d(0,0,0);

}
.shopcart .cartList .title{
  background-color: #f3f5f7;
  height: 40px;
  line-height: 40px;
  padding: 0 18px;
}
.shopcart .cartList .title h2{
  float: left;
  font-size: 14px;
  font-weight: 200;
  color:rgb(7,17,27);
}
.shopcart .cartList .title .clean{
  font-size: 12px;
  color:rgb(0,160,220);
  float: right;
}
.shopcart .cartList .ulBox{
  max-height: 217px;
  overflow: hidden;
}
.shopcart .cartList ul{
  padding: 0 18px;
  overflow: hidden;
  width:100%;
  background-color:white;
  box-sizing: border-box;
}
.shopcart .cartList ul li{
  height:48px;
  border-bottom:1px solid rgba(7,17,27,0.1);
  box-sizing: border-box;
  padding:12px 0;
  position: relative;
}
.shopcart .cartList ul li .name{
  font-size:14px;
  color:rgb(7,17,27);
  line-height:24px;
}
.shopcart .cartList ul li .price{
  position: absolute;
  bottom:12px;
  right: 120px;
  line-height: 24px;
  font-size:14px;
  color:rgb(240,20,20);
  font-weight: 700;
}
.shopcart .cartList ul li .goodsCart-up{
  position: absolute;
  bottom: 0;
  right:0;
}
.shopcart .cartList ul li .price i{
  font-weight: 200;
  font-size: 10px;
  font-style: normal;
}
.shopcart .cartList.fold-enter-active,.shopcart .cartList.fold-leave-active{
  transition: all 0.5s;
}
.shopcart .cartList.fold-enter,.shopcart .cartList.fold-leave-active{
  transform: translate3d(0,0,0);
}
</style>
