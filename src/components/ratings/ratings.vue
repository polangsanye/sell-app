<template>
  <div class="ratings" ref="ratings">
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}</div>
        </div>
        <div class="overview-right">
          <div class="score-wrap">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrap">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrap">
            <span class="title">送达时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect :selectType="selectType" :onlyContent="onlyContent"  :ratings="ratingsMsg" @select="changeSelect" @toggle="changeContent"></ratingselect>
      <div class="rating-wrap">
        <ul>
          <li v-show=" needShow(rating.rateType,rating.text)" v-for="(rating,index) in ratingsMsg" class="rating-item border-1px">
            <div class="avatar">
              <img :src="rating.avatar" width="28" height="28">
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrap">
                <star :size="24" :score:="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                <span class="icon-thumb_up"></span>
                <span v-for="(item,index) in rating.recommend" class="item">{{item}}</span>
              </div>
              <div class="time">{{rating.rateTime|formatDate}}</div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
  </div>
</template>

<script>
  import {formatDate} from "../../commom/js/date.js"
  import star from "../star/star.vue"
  import ratingselect from "../ratingselect/ratingselect.vue"
  import split from "../split/split.vue"
  import BScroll from "better-scroll"
  const All = 2;
  const ERR_OK = 0;
  export default{
    props: ['seller'],
    data(){
      return {
        ratingsMsg: [],
        selectType: All,
        onlyContent: true,
      }
    },
    filters:{
      formatDate(time){
        let date=new Date(time);
        return formatDate(date,'yyyy-MM-dd hh:mm')
      }
    },
    mounted: function () {
      this.$nextTick(function () {
        this.ratings();
      })
    },
    methods: {
      ratings() {
        this.$http.get('/api/ratings').then((res) => {
          res = res.data;
          if (res.errno === ERR_OK) {
            this.ratingsMsg = res.data;
            this.$nextTick(()=>{
              this.scroll=new BScroll(this.$refs.ratings,{
                click:true
              })
            })
          }
        })
      },
      needShow(type,text){
        if(this.onlyContent&&!text){
          return false;
        }
        if(this.selectType===All){
          return true
        }else{
          return type===this.selectType
        }
      },
      changeSelect(type){
        this.selectType=type;
        this.$nextTick(()=>{
          this.scroll.refresh();
        })
      },
      changeContent(){
        this.onlyContent=!this.onlyContent;
        this.$nextTick(()=>{
          this.scroll.refresh();
        })
      }
    },
    components: {
      star, split, ratingselect
    }
  }

</script>

<style>
  @import "../../commom/fonts/style.css";
  @import "ratings.css";
</style>
