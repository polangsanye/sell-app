<template>
  <div class="cartctrl">
    <transition name="move">
      <div class="cart-desc " v-show="food.count>0" @click.stop.prevent="cart_desc">
        <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prevent="cart_add($event)"></div>
  </div>
</template>

<script>
  import Vue from "vue"
  export default{
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      cart_add(event){
        if(!event._constructed){
          return ;
        }
        if (!this.food.count) {
         Vue.set(this.food,'count',1)
        } else {
          this.food.count++
        }
        this.$emit('add',event.target);
      },
      cart_desc(){
        if(this.food.count){
          this.food.count--
        }
      }

    }
  }

</script>

<style>
  @import "cartctrl.css";
</style>
