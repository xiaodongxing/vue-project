<template>
	<div class="ratings" ref="ratings">
    <div  class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <h1 class="title">综合评分</h1>
          <div class="rank">高于周边商家{{seller.rankRate}}</div>
        </div>
        <div class="overview-right">
          <div class="wrapper">
            <h1 class="title">服务态度</h1>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="wrapper">
            <h1 class="title">商品评分</h1>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="wrapper">
            <h1 class="title">送达时间</h1>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <div class="split"></div>
      <rating-select 
        :select-type="selectType" 
        :only-content="onlyContent"
        :ratings="ratings"
        @select="handleChangeSelectType"
        @toggle="handleChangeonlyContent"
      >
      </rating-select>
      <div class="rating-wrapper">
        <ul >
          <li v-for="rating in list" class="rating-item border-1px">
            <div class="avatar">
              <img width="28" height="28" :src="rating.avatar">
            </div>
            <div class="content">
              <div class="info">
                <h1 class="name">{{rating.username}}</h1>
                <span class="time">{{rating.rateTime|formatDate}}</span>
              </div>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend" v-show="rating.recommend||rating.recommend.length">
                <span :class="getClass(rating.rateType)"></span>
                <span v-for="item in rating.recommend" class="recommend-item">{{item}}</span>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div> 
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import {formatDate} from 'common/js/date'
import star from 'components/star/star'
import ratingSelect from 'components/ratingSelect/ratingSelect'
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;
const ERR_OK = 0
export default {
  components: {
    star,
    ratingSelect
  },
  props: {
    seller: {
      type: Object
    }
  },
  data() {
    return {
      showFlag: false,
      selectType: ALL,
      onlyContent: false,
      ratings: []
    }
  },
  created() {
    this.$axios.get('/api/ratings').then((res)=>{
      res = res.data
      if(res.errno === ERR_OK){
        this.ratings = res.data
        this.$nextTick(() => {
          if(!this.scroll){
            this.scroll = new BScroll(this.$refs.ratings,{click:true})
          }else{
            this.scroll.refresh()
          }
        })
      }
    })
  },
  computed: {
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
.ratings
  position: absolute
  top: 174px
  bottom: 0
  width: 100%
  overflow: hidden
  .overview
    display: flex
    padding: 18px 0
    .overview-left
      padding: 6px 0 0
      flex: 0 0 137px
      width: 137px
      border-right: 1px solid rgba(7,17,27,0.1)
      text-align: center
      @media only screen and (max-width:320px)
        flex: 0 0 120px
        width: 120px
      .score
        line-height: 28px
        font-size: 24px
        color: rgb(255,153,0)
      .title
        margin:6px 0 8px
        line-height: 12px
        font-size: 12px
        color: rgb(7,17,27)
      .rank
        font-size: 10px
        color: rgb(147,153,159)
        line-height: 10px
    .overview-right
      flex: 1
      padding: 6px 0 0 24px
      @media only screen and (max-width:320px)
        padding-left: 6px
      .wrapper
        font-size: 12px
        text-align: center
        display: flex
        margin-bottom: 8px
        .title
          color: rgb(7,17,27)
          margin-right: 12px
          line-height: 18px
        .score
          color: rgb(255,153,0)
          margin-left: 12px
          line-height: 18px
        .delivery
          color: rgb(147,153,159)
          line-height: 18px
  .rating-wrapper
    padding: 0 18px
    .rating-item
      padding: 18px 18px 18px 40px
      border-1px(rgba(7,17,27,0.1))
      .avatar
        float:left
        margin-left: -40px
        img
         border-radius: 50%
      .content
        .info
          display: flex
          justify-content: space-between
          font-size: 10px
          line-height: 12px
          .name
            color: rgb(7,17,27)
          .time
            color: rgb(147,153,159)
        .star-wrapper
          display: flex
          margin: 4px 0 6px
          .delivery
            margin-left: 6px
            font-size: 10px
            line-height: 12px
            color: rgb(147,153,159)
        .text
          margin-bottom: 8px
          font-size: 12px
          line-height: 18px
          font-weight: 600
          color: rgb(7,17,27)
        .recommend
          font-size: 0
          .icon-thumb_up,.icon-thumb_down
            font-size: 12px
            line-height: 16px
            margin-right: 8px
          .icon-thumb_up
            color: rgb(0,160,220)
          .icon-thumb_down
            color: rgb(147,153,159)
          .recommend-item
            display: inline-block
            padding: 0 6px
            font-size: 9px
            line-height: 16px
            margin: 0 8px 4px 0
            border: 1px solid rgba(7,17,27,0.1)
</style>