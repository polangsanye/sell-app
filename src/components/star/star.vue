<template>
  <div class="star" :class="starType">
    <span v-for="(itemClass,index) in itemClasses" :class="itemClass" class="star-item"></span>
  </div>
</template>

<script>
  const LENGTH = 5;
  const CLS_ON = 'on';
  const CLS_HALF = 'half';
  const CLS_OFF = 'off';

  export default{
    data(){
      return {}
    },
    props: ['size', 'score'],
    computed: {
      starType(){
        return 'star-' + this.size
      },
      itemClasses() {
        let result = [];
        let score = Math.floor(this.score * 2) / 2;
        let hasDecimal = score % 1 !== 0;
        let integer=Math.floor(score);
        for(let i=0;i<integer;i++){
            result.push(CLS_ON);
        }
        if(hasDecimal){
          result.push(CLS_HALF);
        }
        while (result.length<LENGTH){
          result.push(CLS_OFF);
        }
        return result;
      }

    }
  }

</script>

<style>
  @import "star.css";
</style>
