<template>
  <div class="goods">
    <div class="menu-wrap" ref="menuWrap">
      <ul>
        <li v-for="(item,index) in goodsMsg" class="menu-item" :class="{'current':currentIndex==index}"
            @click="selectMenu(index,$event)">
         <span class="text border-1px">
            <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
         </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrap" ref="foodsWrap">
      <ul>
        <li v-for="(item,index) in goodsMsg" class="food-list foot-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="(food,index) in item.foods" class="food-item" @click="selectedFood(food,$event)">
              <div class="icon">
                <img width="57" height="57" :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}</span><span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span><span v-if="food.oldPrice"
                                                                class="old">￥{{food.oldPrice}}</span>
                </div>
                <div class="cart-wrap">
                  <cartctrl  :food="food" v-on:add="_drop"></cartctrl>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcar ref="shopcar" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice" :select-foods="selectFoods"></shopcar>
    <fooddetail @add="_drop" :food="selectFood" ref="shangpin"></fooddetail>
  </div>
</template>

<script>
  import cartctrl from "../cartctrl/cartctrl.vue"
  import shopcar from "../shopcar/shopcar.vue"
  import BScroll from "better-scroll";
  import fooddetail from "../fooddetail/fooddetail.vue"

  const ERR_OK = 0;

  export default{
    data(){
      return {
        goodsMsg: [],
        classMap: ['decrease', 'discount', 'guarantee', 'invoice', 'special'],
        listHeight: [],
        scrollY: 0,
        selectFood: {},
        a:''
      }
    },

    props: ['seller'],

    mounted: function () {
      this.$nextTick(function () {
        this.goods();
      })
    },
    components: {shopcar, cartctrl, fooddetail},
    computed: {
      currentIndex(){
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || (height1 <= this.scrollY && this.scrollY < height2)) {
            return i
          }
        }
        return 0;
      },
      selectFoods(){
        let foods = [];
        this.goodsMsg.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          })
        });
        return foods

      }
    },
    methods: {
      goods: function () {
        this.$http.get('/api/goods').then((res) => {
          res = res.data;
          if (res.errno == ERR_OK) {
            this.goodsMsg = res.data;
            this.$nextTick(function () {
              this._initScroll();
              this._calculateHeight();
            })
          }
        })
      },
      _initScroll: function () {
        this.menuScroll = new BScroll(this.$refs.menuWrap, {
          click: true
        });
        this.foodsScroll = new BScroll(this.$refs.foodsWrap, {
          click: true,
          probeType: 3
        });
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.floor(pos.y));
        })
      },
      selectMenu: function (index, event) {
        if (!event._constructed) {
          return;
        }
        let foodList = this.$el.querySelectorAll('.foot-list-hook');
        let el = foodList[index];
        this.foodsScroll.scrollToElement(el, 300);
      },
      _calculateHeight: function () {
        let foodList = this.$el.querySelectorAll('.foot-list-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height)
        }

      },
      selectedFood: function (food, event) {
        if (!event._constructed) {
          return;
        }
        this.selectFood = food;
        this.$refs.shangpin.show();
      },
      _drop(target){
        this.$refs.shopcar.drop(target);
      }
    }

  }
</script>


<style>
  @import "goods.css";
</style>
