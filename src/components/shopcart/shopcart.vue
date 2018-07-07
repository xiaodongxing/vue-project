<template>
  <div class="shopcart">
    <div class="content">
      <div class="left" :class="{highlight: totalCount>0}">
        <div class="logo-wrapper">
          <div class="logo">
            <i class="icon-shopping_cart"></i>
          </div>
          <div class="num" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <div class="price">￥{{totalPrice}}</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="right" :class="payClass">{{payDesc}}</div>
    </div>
    <div class="ball-container">
      <transition-group
       name="drop"
       tag="div"
       @before-enter="beforeEnter"
       @enter="dropEnter"
       @after-enter="afterEnter"
      >
        <div v-for="(ball,index) in balls" v-show="ball.show" class="ball" :key="index">
          <div class="inner inner-hook"></div>
        </div>
      </transition-group>
      
    </div>
  </div>
</template>

<script>

export default {
  props: {
    selectFoods: {
      type: Array,
      default() {
        return []
      }
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      balls: [{show: false},{show: false},{show: false},{show: false},{show: false}],
      dropBalls: []
    }
  },
  computed: {
    totalPrice() {
      let total = 0;
      this.selectFoods.forEach((food) => {
        total += food.price * food.count
      })
      return total
    },
    totalCount() {
      let count = 0;
      this.selectFoods.forEach((food) => {
        count += food.count
      })
      return count
    },
    payDesc() {
      if(this.totalPrice === 0) {
        return `￥${this.minPrice}元起送`
      }else if(this.totalPrice < this.minPrice) {
        let diff = this.minPrice - this.totalPrice
        return `还差￥${diff}元起送`
      }else{
        return '去结算'
      }
    },
    payClass() {
      return this.totalPrice < this.minPrice ? '' : 'enough'
    }
  },
  methods: {
    drop(el) {
      for(let i=0;i<this.balls.length;i++){
        let ball = this.balls[i]
        if(!ball.show){
          ball.show = true
          ball.el = el
          this.dropBalls.push(ball)
          break
        }
      }
    },
    beforeEnter(el) {
      let count = this.balls.length
      while(count--){
        let ball = this.balls[count]
        if(ball.show){
          let rect = ball.el.getBoundingClientRect();
          let x = rect.left - 32
          let y = -(window.innerHeight-rect.top - 22)
          el.style.display = ''
          el.style.transform = `translate3d(0,${y}px,0)`
          el.style.webkitTransform = `translate3d(0,${y}px,0)`
          let inner = el.getElementsByClassName('inner-hook')[0]
          inner.style.transform = `translate3d(${x}px,0,0)`
          inner.style.webkitTransform = `translate3d(${x}px,0,0)`
        }
      }
    },
    dropEnter(el,done) {
      /* eslint-disable no-unused-vars */ 
      let rf = el.offsetHeight
      this.$nextTick(() => {
        el.style.transform = 'translate3d(0,0,0)'
        el.style.webkitTransform = 'translate3d(0,0,0)'
        let inner = el.getElementsByClassName('inner-hook')[0]
        inner.style.transform = 'translate3d(0,0,0)'
        inner.style.webkitTransform = 'translate3d(0,0,0)'
        el.addEventListener('transitionend', done);
      })
      
    },
    afterEnter(el) {
      let ball = this.dropBalls.shift()
      if(ball){
        ball.show = false
        el.style.display = 'none'
      }
    }
  } 
}
</script>

<style lang="stylus" scoped>
.shopcart
  position: fixed
  bottom:0
  height: 48px
  line-height: 48px
  width: 100%
  z-index: 100
  .content
    display: flex
    color: rgba(255,255,255,0.4)
    .left
      flex: 1
      background: #141d27
      display: flex
      &.highlight 
        .logo-wrapper
          .logo
            background: rgb(0,160,220)
            .icon-shopping_cart
              color: #fff
        .price
          color: #fff
      .logo-wrapper
        padding: 6px
        margin: 0 12px
        position: relative
        top: -10px
        width: 56px
        height: 56px
        box-sizing: border-box
        border-radius: 50%
        background: #141d27
        .logo
          width: 100%
          height: 100%
          border-radius: 50%
          background: #2b343c
          text-align: center
          
          .icon-shopping_cart
            font-size: 24px
            line-height: 44px
            color: #80858a
        .num
          position: absolute
          top: 0
          right: 0
          width: 24px
          height: 16px
          line-height: 16px
          text-align: center
          border-radius: 16px
          font-size: 9px
          color: #fff
          background: rgb(240,20,20)
          box-shadow 0 4px 8px 0px rgba(0,0,0,0.4)
      .price
        margin: 12px 0
        padding-right: 12px
        line-height: 24px
        font-size: 16px
        color: rgba(255,255,255,0.4)
        font-weight: 700
        border-right: 1px solid rgba(255,255,255,0.1)
      .desc
        margin-left: 12px
        font-size: 10px
    .right
      width:105px
      padding: 0 8px
      text-align: center
      font-size: 14px
      font-weight: 700
      background: #2b343c
      &.enough
        background: #00b43c
        color: #fff
  .ball-container
    .ball
      position: fixed
      left: 32px
      bottom: 22px
      z-index: 200
      &.drop-enter-active
        transition: all 0.4s cubic-bezier(0.49,-0.29,0.75,0.41)
      .inner
        width: 16px
        height: 16px
        border-radius: 50%
        background: rgb(0, 160, 220)
        transition: all 0.4s linear
</style>