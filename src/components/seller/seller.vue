<template>
	<div class="seller" ref="seller">
    <div class="seller-content">
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc border-1px">
          <star :size="36" :score="seller.score"></star>
          <span class="text ratingCount">({{seller.ratingCount}})</span>
          <span class="text sellCount">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block">
            <h2 class="block-title">起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>
              元
            </div>
          </li>
          <li class="block block-border">
            <h2 class="block-title">商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>
              元
            </div>
          </li>
          <li class="block">
            <h2 class="block-title">平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>
              分钟
            </div>
          </li>
        </ul>
        <div class="favorite">
          <span class="icon-favorite" :class="{'active':favorite}" @click="handleClickFavorite"></span>
          <div class="text">{{favoriteText}}</div>
        </div>
      </div>
      <div class="split"></div>
      <div class="bulletin border-1px">
        <h1 class="title">公告与活动</h1>
        <p class="content">{{seller.bulletin}}</p>
        <ul class="support">
          <li class="support-item border-1px" v-for="(item, index) in seller.supports">
            <span class="icon" :class="classMap[item.type]"></span>
            <span class="text">{{item.description}}</span>
          </li>
        </ul>
      </div>
      <div class="split"></div>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" ref="picWrapper">
          <ul class="pic-list" ref="picList">
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic" width="120" height="90">
            </li>
          </ul>
        </div>
      </div>
      <div class="split"></div>
      <div class="infos">
        <h1 class="title border-1px">商家信息</h1>
        <ul>
          <li class="infos-item border-1px" v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>
    </div> 
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import star from 'components/star/star'
export default {
  props: {
    seller: {
      type: Object
    }
  },
  components: {
    star
  },
  data() {
    return {
      favorite: false
    }
  },
  computed: {
    favoriteText() {
      return this.favorite ? '已收藏' : '收藏'
    }
  }, 
  created() {
    this.classMap = ['decrease','discount','special','invoice','guarantee']
  },
  mounted() {
    this._initScroll()
    this._initPicsScroll()
  },
  watch: {
    seller() {
      this._initScroll()
      this._initPicsScroll()
    }
  },
  methods: {
    _initScroll() {
      if(!this.scroll) {
        this.scroll = new BScroll(this.$refs.seller, {
          click: true
        });
      }else{
        this.scroll.refresh();
      }
    },
    _initPicsScroll() {//图片横向滚动
      if(this.seller.pics){
        let picWidth = 120;
        let margin= 6;
        let width = (picWidth + margin) * this.seller.pics.length - margin
        this.$refs.picList.style.width = width+ 'px'
        this.$nextTick(() => {
          if(!this.picScroll){
            this.picScroll = new BScroll(this.$refs.picWrapper,{
              scrollX: true,
              eventPassthrough: 'vertical' 
            })
          }else{
            this.picScroll.refresh()
          }
          
        })
      }
    },
    handleClickFavorite() {
      this.favorite = !this.favorite
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/mixin'
.seller
  position: absolute
  top: 174px
  bottom: 0
  left: 0
  width: 100%
  overflow: hidden
  .overview, .bulletin
    .title
      margin: 18px 0 8px
      font-size: 14px
      color: rgb(7,17,27)
      line-height: 14px
  .overview
    position: relative
    overflow: hidden
    padding: 0 18px
    .desc
      position: relative
      display: flex
      padding-bottom: 18px
      border-1px(rgba(7,17,27,0.1))
      .text
        font-size: 10px
        color: rgb(7,17,27)
        line-height: 18px
        &.ratingCount
          margin: 0 12px 0 8px
    .remark
      display: flex
      padding: 18px 0
      .block
        flex: 1
        text-align: center
        &.block-border
          border-left: 1px solid rgba(7,17,27,0.1)
          border-right: 1px solid rgba(7,17,27,0.1)
        .block-title
          margin-bottom: 4px
          font-size: 10px
          color: rgb(147,153,159)
          line-height: 10px
        .content
          font-size: 10px
          font-weight: 200
          color: rgb(7,17,27)
          line-height: 24px
          .stress
            font-size: 24px
    .favorite
      position: absolute
      right: 18px
      top: 18px
      width: 36px
      text-align: center
      .icon-favorite
        color: #d4d6d9
        line-height: 24px
        font-size: 24px
        &.active
          color: rgb(240,20,20)
      .text
        margin-top: 4px
        font-size: 10px
        color: rgb(77,85,93)
        line-height: 10px
  .bulletin
    padding: 0 18px
    .content
      padding: 0 12px 16px
      font-size: 12px
      color: rgb(240,20,20)
      line-height: 24px
      border-1px(rgba(7,17,27,0.1))
    .support-item
      padding: 16px 12px
      border-1px(rgba(7,17,27,0.1))
      font-size: 0
      &:last-child
        border-none()
      .icon
        display: inline-block
        vertical-align: top
        width: 16px
        height: 16px
        margin-right: 6px
        background-size: 16px 16px
        background-repeat: no-repeat
        &.decrease
          bg-image('decrease_4')
        &.discount
          bg-image('discount_4')
        &.guarantee
          bg-image('guarantee_4')
        &.invoice
          bg-image('invoice_4')
        &.special
          bg-image('special_4')
      .text
        font-size: 12px
        color: rgb(7,17,27)
        line-height: 16px
  .pics,.infos
    .title
      padding: 0 0 12px
      font-size: 14px
      color: rgb(7,17,27)
      line-height: 14px
  .pics
    padding: 18px
    .pic-wrapper
      width: 100%
      overflow: hidden
      white-space: nowrap
      .pic-list
        font-size: 0
        .pic-item
          display: inline-block
          margin-right: 6px
          &:last-child
            margin: 0
  .infos
    padding: 18px 18px 0
    overvie
    .title
      border-1px(rgba(7,17,27,0.1))
    .infos-item
      padding: 16px 12px
      font-size: 12px
      color: rgb(7,17,27)
      line-height: 16px
      border-1px(rgba(7,17,27,0.1))
      &:last-child
        border-none()
</style>