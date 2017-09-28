<template lang="html">
  <div class="cartbutton-wrapper">
      <i @click.stop="reduce()" v-if="food.count>0" class="icon iconfont icon-jianhao1"></i>
      <span v-if="food.count>0">{{food.count}}</span>
      <i class="icon iconfont icon-iconfontxinzeng" @click.stop="add($event)"></i>
  </div>
</template>

<script>
  import Vue from 'vue'
  export default{
    props:{
      food:{
        type:Object
      }
    },
    mounted(){
//      console.log(this.food);
    },
    methods:{
      add(event){
        if(!this.food.count){
          Vue.set(this.food,"count",1);
//          this.food.count = 1;
        }else{
          this.food.count++;
        }
        this.$emit('ball-event',event.target);
      },
      reduce(){
        this.food.count--;
      }
    }
  }
</script>

<style lang="less">
  @import '../../static/less/var.less';
    .cartbutton-wrapper{
      font-size:@base*30rem;
      color: @mc;
      span{
        display: inline-block;
        width: @base*40rem;
        color: @fc1;
        line-height:@base*30rem;
        text-align: center;
      }
    }
</style>
