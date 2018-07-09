<template>
  <div class="cartcontrol">
    <transition name="move">
      <div 
        class="cart-decrease icon-remove_circle_outline"
        v-show="food.count>0"
        @click="handleCartDecrease"
      >
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click="handleCartAdd"></div>
  </div>
</template>

<script>
export default {
  props: {
    food: Object
  },
  methods: {
    handleCartAdd(e) {
      if(!this.food.count){
        this.$set(this.food,'count',1)
      }else{
        this.food.count++
      }
      this.$emit('cartadd',e.target)
    },
    handleCartDecrease() {
      this.food.count--
    }
  }
}
</script>

<style lang="stylus" scoped>
  .cartcontrol
    display: flex
    .cart-decrease, .cart-add
      padding: 5px
      font-size: 24px
      line-height: 24px
      color: rgb(0,160,220)
    .cart-count
      padding-top: 6px
      line-height: 24px
      font-size: 10px
      width: 12px
      text-align: center
      color: rgb(147,153,159)
    .move-enter, .move-leave-to
      opacity: 0
      transform: translateX(24px) rotate(180deg) 
    .move-enter-active, .move-leave-active
      transition: all .4s
</style>