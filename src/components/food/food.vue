<template>
  <transition name="move">
    <div class="food" v-show="showFlag" ref="food">
      <div class="food-content">
        <div class="image-header">
          <img :src="food.image">
          <div class="back" @click="handleCloseFood">
            <i class="icon-arrow_lift"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell-count">月售{{food.sellCount}}份</span><span class="rating">好评率{{food.rating}}</span>
          </div>
          <div class="price">
            <span class="new-price"><i>￥</i>{{food.price}}</span>
            <span class="old-price" v-if="food.oldPrice">￥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
              <cart-control :food="food" @cartadd="handleCartAddDrop"></cart-control>
          </div>
          <div class="buy-wrapper">
            <transition name="fade">
              <div class="buy" v-show="!food.count||food.count===0" @click="handleAddShopToCart">加入购物车</div>
            </transition>  
          </div>
        </div>
        <div class="split" v-if="food.info"></div>
        <div class="info" v-if="food.info">
          <h1 class="title">商品介绍</h1>
          <p class="text">{{food.info}}</p>
        </div>
        <div class="split"></div>
        <div class="ratings">
          <h1 class="title">商品评价</h1>
          <rating-select 
          :select-type="selectType" 
          :only-content="onlyContent"
          :desc="desc"
          :ratings="food.ratings"
          @selectType="handleChangeSelectType"
          @onlyContent="handleChangeonlyContent"
          >
          </rating-select>
          <div class="rating-wrapper">
            <ul v-if="ratings&&ratings.length">
              <li v-for="rating in list" class="rating-item border-1px">
                <div class="rating-info">
                  <div class="timer">
                    {{rating.rateTime|formatDate}}
                  </div>
                  <div class="user">
                    <span class="name">{{rating.username}}</span>
                    <img width="12" height="12" class="avatar" :src="rating.avatar">
                  </div>
                </div>
                <p class="rating-content">
                  <span :class="getClass(rating.rateType)"></span>
                  {{rating.text}}
                </p>
              </li>
            </ul>
            <div class="no-rating" v-else>暂无评论</div>
          </div>
        </div>
      </div>
      
    </div>
  </transition>
</template>

<script>
import BScroll from 'better-scroll'
import {formatDate} from 'common/js/date'
import cartcontrol from 'components/cartcontrol/cartcontrol'
import ratingSelect from 'components/ratingSelect/ratingSelect'
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;
export default {
  components: {
    cartControl:cartcontrol,
    ratingSelect
  },
  props: {
    food: {
      type: Object
    }
  },
  data() {
    return {
      showFlag: false,
      selectType: ALL,
      onlyContent: false,
      desc: {
        all: '全部',
        positive: '推荐',
        negative: '吐槽'
      }
    }
  },
  computed: {
    ratings() {
      return this.food.ratings
    },
    list() {
      if(this.onlyContent){
        return this.listType.filter((item) => {
          return item.text !== ""
        })
      }else {
        return this.listType
      }
    },
    listType() {
      if(this.selectType === ALL){
        return this.ratings
      }else if(this.selectType === POSITIVE){
        return this.positive
      }else if(this.selectType === NEGATIVE){
        return this.nagetive
      }
    },
    positive() {
      return this.ratings.filter((item) => {
        return item.rateType === POSITIVE
      })
    },
    nagetive() {
      return this.ratings.filter((item) => {
        return item.rateType === NEGATIVE
      })
    }
  },
  methods: {
    getClass(rateType) {
      return rateType === 0 ? 'icon-thumb_up' : 'icon-thumb_down'
    },
    show() {
      this.selectType = ALL
      this.onlyContent = false
      this.showFlag = true
      this.$nextTick(() => {
        if(!this.scroll){
          this.scroll = new BScroll(this.$refs.food,{click: true})
        }else{
          this.scroll.refresh()
        }
      })
    },
    handleCartAddDrop(el) {
      this.$emit('cartadd',el)
    },
    handleCloseFood() {
      this.showFlag = false
    },
    handleAddShopToCart(e) {
      console.log(this.food)
      if(!this.food.count){
        this.$set(this.food,'count',1)
      }else{
        this.food.count++
      }
      this.$emit('cartadd',e.target)
    },
    handleChangeSelectType(index) {
      this.selectType = index
    },
    handleChangeonlyContent() {
      this.onlyContent = !this.onlyContent
    }
  },
  filters: {
    formatDate(time) {
      let date = new Date(time)
      return formatDate(date,'yyyy-MM-dd hh:mm')
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/mixin'
.food
  position: fixed
  left: 0
  top: 0
  right: 0
  bottom: 48px
  z-index: 30
  background: #fff
  &.move-enter, &.move-leave-to
    transform: translate3d(100%,0,0)
  &.move-enter-active, &.move-leave-active
    transition: all 0.5s
  .image-header
    position: relative
    width: 100%
    padding-bottom: 100%
    height: 0
    img
      width: 100%
    .back
      position: absolute
      top: 10px
      left: 0
      .icon-arrow_lift
        display: block
        padding: 10px
        font-size: 20px
        color: #fff
  .content
    position: relative
    .title
      font-weight: 700
    .detail
      font-size: 10px
      line-height: 10px
      color: rgb(147,153,159)
      margin: 8px 0 18px
      .sell-count
        margin-right: 12px
    .price
      font-size: 0
      line-height: 14px
      font-weight: 700
      .new-price
        font-size: 14px
        margin-right: 8px
        color: rgb(240,20,20)
      .old-price
        font-size: 10px
        color: rgb(147,153,159)
        text-decoration: line-through
    .cartcontrol-wrapper
      position: absolute
      right: 18px
      bottom: 18px
    .buy-wrapper
      position: absolute
      right: 18px
      bottom: 24px
      .buy
        width: 74px
        height: 24px
        line-height: 24px
        border-radius: 12px
        color: #fff
        font-size: 10px
        text-align: center
        background-color: rgb(0,160,220)
      .fade-enter, .fade-leave-to
        opacity: 0
      .fade-enter-active, .fade-leave-active
        transition: all 0.1s
  .content,.info
    padding: 18px
    .title
      fong-size: 14px
      color: rgb(7,17,27)
      line-height: 14px
  .info
    .title
      margin-bottom: 8px
    .text
      font-size: 12px
      color: rgb(77,85,93)
      line-height: 24px
      padding: 0 8px
  .ratings
    padding-top: 18px
    .title
      margin-left:18px
      fong-size: 14px
      color: rgb(7,17,27)
      line-height: 14px
    .rating-wrapper
      padding: 0 18px
      .rating-item
        padding: 16px 0
        border-1px(rgba(7,17,27,0.1))
        .rating-info
          overflow: hidden
          line-height:12px
          font-size: 10px
          color: rgb(147,153,159)
          margin-bottom: 6px
          .timer
            float: left
          .user
           float: right
           .name
            margin-right: 6px
        .rating-content
          line-height: 16px
          color: rgb(7,17,27)
          fons-size: 12px
          .icon-thumb_up
            font-size: 12px
            color: rgb(0,160,220)
            line-height: 24px
          .icon-thumb_down
            font-size: 12px
            color: rgb(147,153,159)
            line-height: 24px
      .no-rating
        padding: 16px 0
        font-size: 12px 
        color: rgb(7,17,27)
</style>