<template>
  <div id="app">
    <v-header :seller="sellermsg"></v-header>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">评论</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/sellers">商家</router-link>
      </div>
    </div>
    <keep-alive>
      <router-view :seller="sellermsg"></router-view>
    </keep-alive>

  </div>
</template>

<script>
  import header from './components/header/header.vue';
  import {urlParse} from './commom/js/util'
  const ERR_OK = 0;
  export default {
    data(){
      return {
        sellermsg: {
          id:(()=>{
              let queryParam=urlParse();
              return queryParam.id
          })()
        }

      }
    },
    mounted: function () {
      this.$nextTick(function () {
        this.seller()
      })
    },
    methods: {
      seller: function () {
        this.$http.get('/api/seller?id='+this.sellermsg.id).then((res) => {
          res = res.data;
          if (res.errno === ERR_OK) {
            this.sellermsg=Object.assign({},this.sellermsg,res.data);
          }
        })
      }
    },
    components: {
      'v-header': header
    }

  }
</script>

<style>

  @import "commom/css/mixmin.css";

  .tab {
    height: 40px;
    width: 100%;
    display: flex;
  }

  .tab .tab-item {
    flex: 1;
    text-align: center;
    line-height: 40px;
  }

  .tab .tab-item a {
    display: block;
  }

  .router-link-active {
    color: red
  }
</style>
