<template>
  <div class="comment">
    <div class="commentTop">
      <div class="commentScore">
        <h1>{{seller.score}}</h1>
        <h3>综合评分</h3>
        <p>高于周边商家{{seller.rankRate}}%</p>
      </div>
      <div class="commentService">
        <ul>
          <li>
            <h5>服务态度</h5>
            <star :size="36" :score="seller.serviceScore" class="commentstar"></star>
            <p>{{seller.serviceScore}}</p>
          </li>
          <li>
            <h5>食品质量</h5>
            <star :size="36" :score="seller.foodScore" class="commentstar"></star>
            <p>{{seller.foodScore}}</p>
          </li>
          <li>
            <h5>送达时间</h5>
            <p>{{seller.deliveryTime}}分钟</p>
          </li>
        </ul>
      </div>
    </div>
    <div class="comment-line">

    </div>
    <div class="com-satisfaction">
      <div class="satisLogo">
        <span v-on:click="allShow">全部<i>{{all}}</i></span>
        <span v-on:click="satisfationShow">满意<i>{{satis}}</i></span>
        <span v-on:click="nosatisfationShow">不满意<i>{{nosatis}}</i></span>
      </div>
    </div>
    <div class="onlyComment">
      <h2><span class="iconfont icon-check_circle" :class="onlyShow" v-on:click="onlyhave"></span>只看有内容的评论</h2>
    </div>
    <div class="commentBottom" ref="commentSlide">
      <ul>
        <li v-for="item in ratings" v-show="havecomment ? (item.text) : (nosatisfation == 'aaa' ? true : item.rateType == nosatisfation)">
          <div class="infor-logo">
            <img :src="item.avatar" />
          </div>
          <div class="comment-infor">
            <h5>{{item.username}}<span>{{item.rateTime | nowTime}}</span></h5>
            <p><star :size="36" :score="item.score"></star><span v-show="item.deliveryTime">{{item.deliveryTime}}分钟送达</span></p>
          </div>
          <div class="comment-content">
            {{item.text}}
          </div>
          <div class="conmment-type">
            <i class="iconfont" :class="item.rateType == 1 ? isLow : isGood"></i>
            <span v-for="item1 in item.recommend">{{item1}}</span>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script type="text/javascript">
import Vue from 'Vue'
import star from "@/components/star/star"
import bs from 'better-scroll';
  export default{
    name:"comment",
    props:{
      seller:{
        type:Object
      }
    },
    data(){
      return {
        ratings:[],
        nosatisfation:'aaa',
        havecomment:false
      }
    },
    created:function(){
      var _this = this
      this.$http.get('/api/ratings').then(function(respone){
        if(respone.data.err == 0){
          _this.ratings = respone.data.data;
          _this.$nextTick(()=>{
            _this.initScroll();
          })
        }
      })
    },
    components:{
      star:star
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
    methods:{
      allShow:function(){
        this.nosatisfation = 'aaa';
      },
      satisfationShow:function(){
        this.nosatisfation = 0;
      },
      nosatisfationShow:function(){
        this.nosatisfation = 1;
        console.log(this.nosatisfation)
      },
      onlyhave:function(){
        this.havecomment = !this.havecomment
      },
      initScroll(){
        this.commentSlide = new bs(this.$refs.commentSlide,{
          click:true
        })
      }
    },
    computed:{
      satis:function(){
        let num = 0;
        for(var i = 0;i<this.ratings.length;i++){
          if(this.ratings[i].rateType == 0){
            num++;
          }
        }
        return num;
      },
      all:function(){
        let num = 0;
        for(var i = 0;i<this.ratings.length;i++){
          num++;
        }
        return num;
      },
      nosatis:function(){
        let num = 0;
        for(var i = 0;i<this.ratings.length;i++){
          if(this.ratings[i].rateType == 1){
            num++;
          }
        }
        return num;
      },
      onlyShow:function(){
        if(this.havecomment){
          return 'onlyShow'
        }
      },
      commentShow:function(){

      },
      isLow:function(){
        return 'icon-thumb_down';
      },
      isGood:function(){
        return 'icon-thumb_up';
      }
    }
  }
</script>

<style>
.commentTop{
  padding: 18px 0;
  box-sizing: border-box;
  width: 100%;
  display: flex;
}
.commentTop .commentScore{
  width: 138px;
  box-sizing: border-box;
  border-right: 1px solid gray;
}
.commentTop .commentScore h1{
  font-size: 24px;
  color:rgb(255,153,0);
  line-height: 28px;
  text-align: center;
}
.commentTop .commentScore h3{
  font-size: 12px;
  color:rgb(7,17,27);
  line-height: 12px;
  text-align: center;
  margin: 6px 0 8px 0;
}
.commentTop .commentScore p{
  font-size: 10px;
  color:rgb(7,17,27);
  line-height: 10px;
  text-align: center;
  margin-bottom: 6px;
}
.commentTop .commentService{
  flex-grow: 1;
  padding: 0 24px;
}
.commentTop .commentService ul li{
  display: flex;
  justify-content: flex-start;
}
.commentTop .commentService ul li:nth-child(2){
  margin: 8px 0;
}
.commentTop .commentService ul h5{
  font-size: 12px;
  color:rgb(7,17,27);
  line-height: 18px;
  margin-right:12px;
}
.commentTop .commentService ul p{
  font-size: 12px;
  color:rgb(147,153,159);
  line-height: 18px;
}
.commentTop .commentService ul .commentstar{
  margin-right:12px;
}
.comment-line{
  height: 17px;
  width: 100%;
  background-color: #f3f5f7;
}
.com-satisfaction{
  width: 100%;
}
.com-satisfaction .satisLogo{
  margin: 0 18px;
  padding: 18px 0;
  box-sizing: border-box;
  display: flex;
  justify-content: flex-start;
  border-bottom: 1px solid gray;
}
.com-satisfaction span{
  width: 60px;
  height: 32px;
  text-align: center;
  font-size: 12px;
  line-height: 32px;
  cursor: pointer;
}
.com-satisfaction span i{
  font-style:normal;
  font-size:10px;
  margin-left: 2px;
}
.com-satisfaction span:nth-child(1){
  background-color: #00a0dc;
  color:white;
}
.com-satisfaction span:nth-child(2){
  background-color: #ccecf8;
  color:#4d555d;
  margin: 0 9px;
}
.com-satisfaction span:nth-child(3){
  background-color: #e9ebec;
  color:#4d555d;
}
.onlyComment{
  border-bottom: 1px solid gray;
}
.onlyComment h2{
  font-size: 12px;
  color:#93999f;
  padding: 18px;
}
.onlyComment h2 span{
  margin-right: 2px;
}
.onlyComment h2 span.onlyShow{
  color: #00a0dc;
}
.commentBottom{
  padding:0 18px;
  width: 100%;
  box-sizing: border-box;
  max-height: 241px;
  overflow: hidden;
}
.commentBottom ul li{
  position: relative;
  padding:18px 18px 18px 40px;
  border-bottom: 1px solid gray;
}
.commentBottom ul li .comment-infor h5{
  font-size: 10px;
  color:rgb(7,17,27);
  line-height: 12px;
  margin-bottom: 4px;
}
.commentBottom ul li .comment-infor h5 span{
  float: right;
}
.commentBottom ul li .comment-infor p{
  display:flex;
  flex-wrap: nowrap;
  margin-bottom: 6px;
}
.commentBottom ul li .comment-infor p span{
  font-size: 10px;
  font-weight: 200;
  color:rgb(147,153,159);
  line-height: 20px;
}
.commentBottom ul li .comment-content{
  font-size: 12px;
  color: rgb(7,17,27);
  line-height: 18px;
  margin-bottom: 6px;
}
.commentBottom ul li .infor-logo{
  position: absolute;
  border-radius: 50%;
  overflow:hidden;
  left:0px;
  top:18px;
}
.commentBottom ul li .infor-logo img{
  width: 28px;
  height: 28px;
}
.commentBottom ul li .conmment-type{
  display: flex;
  flex-wrap: wrap;
}
.commentBottom ul li .conmment-type span{
  padding: 0 6px;
  max-width: 63px;
  height: 16px;
  font-size: 9px;
  color:rgb(147,153,159);
  line-height: 16px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  margin: 0 8px;
  box-sizing: border-box;
  border:1px solid rgba(7,17,27,0.1);
  border-radius: 2px;
  background-color: rgb(255,255,255);
}
</style>
