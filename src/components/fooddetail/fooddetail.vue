<template>
  <transition name="move">
    <div class="fooddetail" v-show="showFlag" ref="fooddetail">
      <div class="food-content">
        <div class="image-header">
          <img :src="food.image">
          <div class="back" @click="showFlag=false">
            <i class="icon-arrow_lift"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail-count">
            <span class="sell-count">月售{{food.sellCount}}</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <div class="price">
            <span class="now">￥{{food.price}}</span>
            <span v-if="food.oldPrice" class="old">￥{{food.oldPrice}}</span>
          </div>
          <div class="cart-ctrl-wrap">
            <cartctrl @add="addFood" :food="food"></cartctrl>
          </div>
          <div class="buy" @click.stop.prevent="addfirst" v-show="!food.count||food.count===0">加入购物车</div>
        </div>
        <split v-show="food.info"></split>
        <div class="info" v-show="food.info">
          <h1 class="title">商品信息</h1>
          <p class="text">{{food.info}}</p>
        </div>
        <split></split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <ratingselect :selectType="selectType" :onlyContent="onlyContent" :desc="desc" :ratings="food.ratings" @select="changeSelect" @toggle="changeContent"></ratingselect>
          <div class="rating-wrap">
            <ul v-if="food.ratings && food.ratings.length">
              <li  v-show="needShow(rating.rateType,rating.text)" v-for="(rating,index) in food.ratings" class="rating-item border-1px">
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <img :src="rating.avatar" class="avatar" width="12" height="12">
                </div>
                <div class="time">{{rating.rateTime|formatDate}}</div>
                <p class="text">
                  <i :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></i><span>{{rating.text}}</span>
                </p>
              </li>
            </ul>
            <div class="no-ratings" v-else>
              暂无评价
            </div>
          </div>
        </div>

      </div>
    </div>
  </transition>

</template>
<script>
  import {formatDate} from "../../commom/js/date.js"
  import BScroll from "better-scroll"
  import cartctrl from "../cartctrl/cartctrl.vue"
  import ratingselect from "../ratingselect/ratingselect.vue"
  import split from "../split/split.vue"
  import Vue from "vue"


  const All = 2;

  export default{
    data(){
      return {
        showFlag: false,
        selectType: All,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      }
    },
    props: {
      food: {
        type: Object
      }
    },
    filters:{
        formatDate(time){
          let date=new Date(time);
          return formatDate(date,'yyyy-MM-dd hh:mm')
        }
    },
    components: {
      cartctrl, split, ratingselect
    },
    methods: {
      show(){
        this.showFlag = true;
        this.selectType = All;
        this.onlyContent = true;
        this.$nextTick(() => {
          if (!this.foodScroll) {
            this.foodScroll = new BScroll(this.$refs.fooddetail, {
              click: true
            });
          } else {
            this.foodScroll.refresh();
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

      addfirst(event){
        if (!event._constructed) {
          return
        }
        Vue.set(this.food, 'count', 1);
        this.$emit('add',event.target);
      },
      addFood(target){
          this.$emit('add',target)
      },

      changeSelect(type){
          this.selectType=type;
          this.$nextTick(()=>{
            this.foodScroll.refresh();
          })
      },
      changeContent(){
        this.onlyContent=!this.onlyContent;
        this.$nextTick(()=>{
          this.foodScroll.refresh();
        })
      }

    }
  }
</script>
<style>
  @import "../../commom/fonts/style.css";
  @import "fooddetail.css";
</style>
