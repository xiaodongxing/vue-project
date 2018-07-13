<template>
	<div class="goods">
    <div class="menu-wrapper">
     <ul class="content">
       <li class="menu-item" v-for="(item,index) in goods" :class="{'current': currentIndex===index}" @click="handleSelectMenu(index)">
         <span class="text border-1px"><span v-show="item.type>0" :class="classMap[item.type]" class="icon"></span>{{item.name}}</span>
       </li>
     </ul>
    </div>
    <div class="foods-wrapper">
     <ul class="content">
       <li v-for="item in goods" class="food-list" ref="food-list">
         <h1 class="title">{{item.name}}</h1>
         <ul>
           <li v-for="food in item.foods" class="food-item border-1px" @click="handleSelectFood(food)">
            <div class="icon">
              <img :src="food.icon" width="57" height="57">
            </div>
            <div class="content">
              <h2 class="name">{{food.name}}</h2>
              <p class="desc" v-if="food.description">{{food.description}}</p>
              <div class="extra">
                <span>月售{{food.sellCount}}份</span>
                <span>好评率{{food.rating}}%</span>
              </div>
              <div class="price">
                <span class="new-price"><i>￥</i>{{food.price}}</span>
                <span class="old-price" v-if="food.oldPrice">￥{{food.oldPrice}}</span>
              </div>
              <div class="cartcontrol-wrapper">
                <cart-control :food="food" @cartadd="handleCartAddDrop"></cart-control>
              </div>
            </div>
           </li>
         </ul>
       </li>
     </ul>
    </div>
    <shopcart
      :delivery-price="seller.deliveryPrice"
      :min-price="seller.minPrice"
      :select-foods="selectFoods"
      ref="shopcart"
    >
    </shopcart>
    <food :food="selectFood" ref="food" @cartadd="handleCartAddDrop"></food>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import cartcontrol from 'components/cartcontrol/cartcontrol'
import shopcart from 'components/shopcart/shopcart';
import food from 'components/food/food';
const ERR_OK = 0;
export default {
  components: {
    cartControl: cartcontrol,
    shopcart,
    food
  },
  props:{
    seller: Object
  },
  data() {
    return {
      goods: [],
      listHeight: [],
      scrollY: 0,
      currentIndex: 0,
      flag: true,
      selectFood: {}
    }
  },
  mounted() {
    this.classMap = ['decrease','discount','special','invoice','guarantee']
    this.getGoodsData()
  },
  watch: {
    scrollY() {
      for(let i = 0; i < this.listHeight.length; i++){
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[i+1]
        if(!height2)return 0
        if((this.scrollY >= height1 && this.scrollY < height2)){
          this.currentIndex= i
        }
      }
    }
  },
  computed: {
    selectFoods() {
      let foods = []
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if(food.count){
            foods.push(food)
          }
        })
      })
      return foods
    }
  },
  methods: {
    handleCartAddDrop(el) {
      //体验优化，异步执行下落动画
      this.$nextTick(() => {
        this.$refs.shopcart.drop(el)
      })
      
    },
    handleSelectFood(food) {
      this.selectFood = food
      this.$refs.food.show()
    },
    _initScroll() {
      this.meunScroll = new BScroll('.menu-wrapper', {click:true})
      this.foodScroll = new BScroll('.foods-wrapper',{click:true,probeType:3})
      this.foodScroll.on('scroll',(pos) => {
        if(this.flag){
          this.scrollY = Math.abs(Math.round(pos.y))
        }
        
      })
      this.foodScroll.on('scrollEnd',(pos) => {
          this.flag = true
      })
    },
    _calculateHeight() {
      let foodList = this.$refs['food-list']
      let height = 0;
      this.listHeight.push(height);
      for(let i = 0; i < foodList.length; i++){
        let item = foodList[i]
        height += item.clientHeight;
        this.listHeight.push(height);
      }
    },
    getGoodsData() {
      this.$axios.get('/api/goods').then(this.getGoodsDataSucc)
    },
    getGoodsDataSucc(res) {
      res = res.data
      if(res.errno === ERR_OK){
        this.goods = res.data
        this.$nextTick(() => {
          this._initScroll()
          this._calculateHeight()
        })
      }
    },
    handleSelectMenu(index) {
      let element = this.$refs['food-list'][index]
      this.flag = false
      this.currentIndex = index
      this.foodScroll.scrollToElement(element,300)
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/mixin'
.goods
  display: flex
  position: absolute
  top: 174px
  bottom: 46px
  width: 100%
  overflow: hidden
  .menu-wrapper
    flex: 0 0 80px
    width: 80px
    background: #f3f5f7
    .menu-item
      display: table
      width: 56px
      height: 54px
      padding: 0 12px
      line-height: 14px
      &.current
        background: #fff
        position: relative
        z-index: 100
        margin-top: -1px
        .text
          border-none()
      .icon
        display: inline-block
        vertical-align: top
        width: 12px
        height: 12px
        margin-right: 2px
        background-size: 12px 12px
        background-repeat: no-repeat
        &.decrease
          bg-image('decrease_3')
        &.discount
          bg-image('discount_3')
        &.guarantee
          bg-image('guarantee_3')
        &.invoice
          bg-image('invoice_3')
        &.special
          bg-image('special_3')
      .text
        display: table-cell
        vertical-align: middle
        font-size: 12px
        border-1px(rgba(7,17,27,0.1))
      &:last-child .text
        border-none()
  .foods-wrapper
    flex: 1
    .title
      padding-left: 14px
      height: 26px
      line-height: 26px
      background-color: #f3f5f7
      color: rgb(147, 153,159)
      font-size: 12px
      border-left: 2px solid #d9dde1
    .food-item
      display: flex
      margin: 0 18px
      padding: 18px 0
      border-1px(rgba(7,17,27,0.1))
      &:last-child
        border-none()
        margin-bottom: 0
      .icon
        //flex: 0 0 57px
        margin-right: 10px
      .content
        position: relative
        flex: 1
        .name
          margin-top: 2px
          font-size: 14px
          line-height: 14px
          color: rgb(7,17,27)
        .desc,.extra
          font-size: 10px
          color: rgb(147,153,159)
          line-height: 10px
          margin-top: 8px
        .extra
          margin-top: 8px
          font-size: 0
          span
            font-size: 10px
            &:first-child
              margin-right: 12px
        .price
          font-size: 0
          line-height: 24px
          font-weight: 700
          .new-price
            margin-right: 8px
            font-size: 14px
            color: rgb(240,20,20)
          .old-price
            font-size: 10px
            color: rgb(147,153,159)
            text-decoration: line-through
        .cartcontrol-wrapper
          position: absolute
          right: 0
          bottom: 0
            
</style>