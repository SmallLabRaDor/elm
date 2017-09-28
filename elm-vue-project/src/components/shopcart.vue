<template lang="html">
    <div class="shopcart-wrapper" @click="isShow=!isShow">
          <div class="cartlogo" :class="{animated:bounceInFlag,bounceIn:bounceInFlag}">
              <div class="out">
                  <span class="total" v-if="totalCount>0">{{totalCount}}</span>
                  <div class="in" :class="{act:totalCount}">
                      <i class="icon iconfont icon-gouwuche"></i>
                  </div>
              </div>
          </div>
          <div class="price-box">
              <span class="price">${{totalPrice}}</span>
              <span class="fee">配送费$7元</span>
          </div>
          <div class="topay" :class="{act:totalPrice}">
               去结算
          </div>
          <transition name="slide">
              <div class="cart-list" v-show="isShow&&selectedFoods.length>0">
                  <div class="cart-list-header">
                      <span class="car">购物车</span>
                      <span class="clear"><i class="icon iconfont icon-lajitong"></i> 清空</span>
                  </div>
                  <ul>
                      <li v-for="food in selectedFoods" class="food-list">
                          <span>{{food.name}}</span>
                          <span class="price">${{food.prite}}</span>
                          <span class="cartbutton-box">
                                <cartbutton :food="food"></cartbutton>
                          </span>
                      </li>
                  </ul>
              </div>
          </transition>

          <!-- 小球的外围盒子 -->
          <transition v-for="(ball,index) in balls" :key="index"
          @before-enter="beforeEnter" @enter="enter"
          @after-enter="afterEnter">
                  <div class="ball-container" v-show="ball.show">
                          <div class="in"></div>
                  </div>
          </transition>
    </div>
</template>

<script>
import cartbutton from "@/components/cartbutton"
export default {
  data(){
    return{
      bounceInFlag:false,
      dropDown:false,
      dropBalls:[],
      balls:[
        {show:false},
        {show:false},
        {show:false},
        {show:false},
        {show:false},
        {show:false},
        {show:false},
        {show:false}
      ],
      isShow:false
    }
  },
   components:{
     cartbutton
   },
   props:{
     selectedFoods:{
       type:Array,
       default(){
         return [
           {count:1,price:10},
           {count:2,price:20}
         ]
       }
     }
   },
  computed:{
     totalCount(){
       let total = 0;
       this.selectedFoods.forEach(food=>{
         total += food.count;
       })
       return total;
     },
      totalPrice(){
        let total = 0;
        this.selectedFoods.forEach(food=>{
          total += food.count * food.price;
        })
        return total;
      }
  },
  methods:{
    ballClick(target){
      for(var i = 0;i < this.balls.length;i ++){
         let ball = this.balls[i];
         if(!ball.show){
           ball.show = true;
           ball.target = target;
           this.dropBalls.push(ball);
           return;
         }
      }
    },
    beforeEnter(el){
      let count = this.balls.length;
      while(count--){
        let ball = this.balls[count];
        if(ball.show){
          let rect = ball.target.getBoundingClientRect();
          let left = rect.left - 35;
          let top = -(window.innerHeight - rect.top - 50);
          console.log(top);
          console.log(left)
          el.style.display = "";
          el.style.webkitTransform = `translate3d(0,${top}px,0)`;
          el.style.transform = `translate3d(0,${top}px,0)`;
          let inner = el.getElementsByClassName("in")[0];
          inner.style.webkitTransform = `translate3d(${left}px,0,0)`;
          inner.style.transform = `translate3d(${left}px,0,0)`;
        }
      }
    },
    enter(el,done){
      let rest = el.offsetHeight;  //触发浏览器重绘
      this.$nextTick(()=>{
        el.style.webkitTransform = "translate3d(0,0,0)";
        el.style.transform = "translate3d(0,0,0)";
        let inner = el.getElementsByClassName("in")[0];
        inner.style.webkitTransform = "translate3d(0,0,0)";
        inner.style.transform = "translate3d(0,0,0)";
        el.addEventListener("transitionend",done);
      })
    },
    afterEnter(el){
      let ball = this.dropBalls.shift();
      if(ball.show){
        ball.show = false;
        el.style.display = "none";

        this.bounceInFlag = true;
        let self = this;
        clearTimeout(timer);
        let timer = setTimeout(()=>{
           self.bounceInFlag = false;
        },750)
      }
    },
  }
}
</script>

<style lang="less">
  @import '../../static/less/var.less';
  .ball-container{
    position: absolute;
    left: 35px;
    top: 0;
    z-index: 200;
    transition: .75s cubic-bezier(.2,-0.37,.86,.34);
    .in{
      transition: .75s;
      width:@base*30rem;
      height:@base*30rem;
      border-radius:50%;
      background: aqua;
    }
  }
  .shopcart-wrapper{
    position: fixed;
    bottom:0;
    left: 0;
    /*z-index: 100;*/
    width: 100%;
    height:@base*96rem;
    background: @bggray1;
    display: flex;
    justify-content: space-between;
    .cartlogo{
      width: @base*160rem;
      position: relative;
      z-index: 10;
      .out{
        width: @base*80rem;
        height: @base*80rem;
        background: @bggray2;
        border-radius: 50%;
        padding: @base*10rem;
        margin-top: @base*-22rem;
        margin-left: @base*32rem;
        position: relative;
        .total{
          padding: 2px 8px;
          background: red;
          color: #fff;
          position: absolute;
          top: -@base*10rem;
          left: @base*70rem;
          border-radius: 50px;
          font-size: @base*6rem;
        }
        .in{
          width: @base*80rem;
          height: @base*80rem;
          background: @bggray1;
          border-radius: 50%;
          text-align: center;
          line-height: @base*80rem;
          i{
            color: @bggray3;
            font-size: @base*50rem;
          }
          &.act {
            background: @mc;
            i{
              color: #fff;
            }
          }
        }
      }
    }
    .price-box{
      flex: 1;
      display: flex;
      flex-direction: column;
      color: #fff;
      position: relative;
      z-index: 10;
      .price{
        font-size: @base*40rem;
      }
      .fee{
        font-size: @base*6rem;
      }
    }
    .topay{
      width: @base*192rem;
      background: @bggray3;
      text-align: center;
      line-height: @base*96rem;
      color: #fff;
      position: relative;
      z-index: 10;
      &.act{
        background: aqua;
        color: #333;
      }
    }
    .cart-list{
      max-height:@base*681rem;
      width: 100%;
      position: absolute;
      bottom:@base*96rem;
      background: #fff;
      overflow: auto;
      .cart-list-header{
        height:@base*81rem;
        background: #f5f5f5;
        padding:@base*5rem @base*20rem;
        line-height:@base*81rem;
        .car{
          padding-left:@base*10rem;
          border-left:@base*3rem solid @mc;
          float: left;
        }
        .clear{
          float: right;
        }
      }
      .food-list{
        height:@base*80rem;
        padding:@base*15rem @base*25rem;
        border-bottom:1px solid @bc1;
        line-height:@base*80rem;
        display: flex;
        justify-content: space-between;
        .cartbutton-box{
          width:@base*155rem;
        }
      }
    }
  }
  .slide-enter-active,.slide-leave-active{
     transition: .5s;
  }
  .slide-enter{
     transform: translateY(100%);
     opacity: 0;
  }
  .slide-leave-active{
    transform: translateY(100%);
    opacity: 0;
  }
</style>
