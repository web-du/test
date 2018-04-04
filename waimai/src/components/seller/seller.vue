<template>
  <div class="sellerBox" ref="sellerListSlide">
    <div class="seller">
      <div class="sellerDetail">
        <div class="sellerName">
          <div class="sellerName-l">
            <h3>{{seller.name}}</h3>
            <p><star :size="36" :score="seller.score"></star><i>({{allcomment}})</i><span>月售{{seller.sellCount}}单</span></p>
          </div>
          <div class="sellerName-r">
            <span class="iconfont icon-favorite" :class="havecollect" v-on:click="collect"></span>
            <p>{{collection}}</p>
          </div>
        </div>
        <div class="deliverydetail">
          <div class="de-l">
            <p>起送价</p>
            <h2>{{seller.minPrice}}<span>元</span></h2>
          </div>
          <div class="de-c">
            <p>商家配送</p>
            <h2>{{seller.deliveryPrice}}<span>元</span></h2>
          </div>
          <div class="de-r">
            <p>平均配送时间</p>
            <h2>{{seller.deliveryTime}}<span>分钟</span></h2>
          </div>
        </div>
      </div>
      <div class="sellerLine"></div>
      <div class="sellerActivity">
        <h2>公告与活动</h2>
        <div class="seller-content">
          {{seller.bulletin}}
        </div>
        <ul>
          <li v-for="item in seller.supports">
            <span class="icon" :class="classArr[item.type]"></span>
            <span class="text">{{item.description}}</span>
          </li>
        </ul>
      </div>
      <div class="sellerLine"></div>
      <div class="sellerPhoto">
        <h2>商家实景</h2>
        <lunbo :images="seller.pics"></lunbo>
      </div>
      <div class="sellerLine"></div>
      <div class="seller-info">
        <h2>商家信息</h2>
        <ul>
          <li v-for="item in seller.infos">{{item}}</li>
        </ul>
      </div>
    </div>
  </div>

</template>


<script type="text/javascript">
import lunbo from '@/components/lunbo/lunbo'
import star from '@/components/star/star'
import bs from 'better-scroll'
  export default{
    name:"seller",
    props:{
      seller:{
        type:Object
      }
    },
    data(){
      return{
        ratings:[],
        collection:'收藏',
        nohave:false
      }
    },
    components:{
      star:star,
      lunbo:lunbo
    },
    created:function(){
      var _this = this
      this.$http.get('/api/ratings').then(function(respone){
        if(respone.data.err == 0){
          _this.ratings = respone.data.data;
          _this.$nextTick(function(){
            _this.initScroll();
          })
        }
      })
      this.classArr = ["decrease","discount","special","invoice","guarantee"];
    },
    computed:{
      allcomment:function(){
        let num = 0;
        for(var i = 0;i<this.ratings.length;i++){
          num++;
        }
        return num;
      },
      initScroll(){
        this.sellerListSlide = new bs(this.$refs.sellerListSlide,{
          click:true
        })
      },
      havecollect(){
        var collect = '';
        if(this.nohave){
          collect = "collect";
        }else{
            collect = "nocollect"
        }
        return collect;
      }
    },
    methods:{
      collect:function(){
        this.nohave = !this.nohave;
        if(this.nohave){
          this.collection = "已收藏";
        }else{
          this.collection = "收藏";
        }

      }
    }
  }
</script>

<style>
.sellerBox{
  width: 100%;
  max-height: 442px;
  overflow: hidden;
}
.sellerDetail{
  padding: 0 18px 18px 18px;
  box-sizing:border-box;
  width: 100%;
}
.sellerName{
  display: flex;
  justify-content: space-between;
  padding: 18px 0;
  border-bottom: 1px solid rgba(7,17,27,0.1);
}
.sellerName .sellerName-l h3{
  font-size: 14px;
  color:rgb(7,17,27);
  line-height: 14px;
  margin-bottom: 8px;
}
.sellerName .sellerName-l p{
  display: flex;
  justify-content: flex-start;
}
.sellerName .sellerName-l p i{
  font-style: normal;
  font-size: 10px;
  color:rgb(77,85,93);
  line-height: 18px;
  margin: 0 12px 0 8px;
}
.sellerName .sellerName-l p span{
  font-size: 10px;
  color:rgb(77,85,93);
  line-height: 18px;
}
.sellerName .sellerName-r span{
  display: block;
  margin: 0 auto 4px;
  text-align: center;
  font-size: 24px;
  line-height: 24px;
}
.sellerName .sellerName-r span.collect{
  color:red;
}
.sellerName .sellerName-r span.nocollect{
  color:rgb(77,85,93);
}
.sellerName .sellerName-r p{
  font-size: 10px;
  color:rgb(77,85,93);
  line-height: 10px;
  text-align: center;
}
.deliverydetail{
  display: flex;
  margin-top: 18px;
}
.deliverydetail div{
  flex-grow: 1;
}
.deliverydetail div.de-c{
  border-left:1px solid rgba(7,17,27,0.1);
  border-right:1px solid rgba(7,17,27,0.1);
}
.deliverydetail p{
  font-size: 10px;
  color:rgb(147,153,159);
  line-height: 10px;
  text-align: center;
  margin-bottom: 4px;
}
.deliverydetail h2{
  font-size: 24px;
  font-weight: 200;
  color:rgb(7,17,27);
  line-height: 24px;
  text-align: center;
}
.deliverydetail h2 span{
  font-size: 10px;
}
.sellerLine{
  height: 17px;
  width: 100%;
  background-color: #f3f5f7;
}
.sellerActivity{
  padding: 18px 18px 0 18px;
}
.sellerActivity h2{
  font-size: 14px;
  color:#07111b;
  margin-bottom: 8px;
}
.sellerActivity .seller-content{
  padding: 0 12px;
  font-size: 12px;
  font-weight: 200;
  color:rgb(240,20,20);
  line-height: 24px;
}
.sellerActivity ul li{
  display: flex;
  padding: 16px 12px;
  box-sizing: border-box;
  border-top: 1px solid rgba(7,17,27,0.1);
}
.sellerActivity .icon{
  width: 16px;
  height: 16px;
  margin-right: 6px;
  background:url('decrease_3@2x.png') 0 0 no-repeat;
  background-size: 16px 16px;
}
.sellerActivity .decrease{
  background-image: url("decrease_3@2x.png");
}
.sellerActivity .discount{
  background-image: url("discount_3@2x.png");
}
.sellerActivity .guarantee{
  background-image: url("guarantee_3@2x.png");
}
.sellerActivity .invoice{
  background-image: url("invoice_3@2x.png");
}
.sellerActivity .special{
  background-image: url("special_3@2x.png");
}
.sellerActivity .text{
  font-size: 12px;
  font-weight: 200;
  line-height: 16px;
  color:rgb(7,17,27);
}
.sellerPhoto{
  padding: 18px 0 18px 18px;
  box-sizing:border-box;
  width: 100%;
}
.sellerPhoto h2{
  font-size: 14px;
  color:#07111b;
  margin-bottom: 12px;
}
.seller-info{
  padding: 18px 18px 0 18px;
  box-sizing: border-box;
  width: 100%;
}
.seller-info h2{
  font-size: 14px;
  color:#07111b;
  margin-bottom: 12px;
}
.seller-info ul li{
  padding: 16px 12px;
  box-sizing: border-box;
  border-top: 1px solid rgba(7,17,27,0.1);
  font-size: 12px;
  font-weight: 200;
  color:rgb(7,17,27);
  line-height: 16px;
}
</style>
