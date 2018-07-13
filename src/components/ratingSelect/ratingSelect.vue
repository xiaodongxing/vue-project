<template>
  <div>
    <div class="rating-type border-1px">
      <span class="type-item positive" :class="{'active':selectType===2}" @click="handleSelect(2)">{{desc.all}}<i class="number" >{{ratings.length}}</i></span>
      <span class="type-item positive" :class="{'active':selectType===0}" @click="handleSelect(0)">{{desc.positive}}<i class="number" >{{positive.length}}</i></span>
      <span class="type-item nagetive" :class="{'active':selectType===1}" @click="handleSelect(1)">{{desc.negative}}<i class="number" >{{nagetive.length}}</i></span>
    </div>
    <div class="switch" :class="{'on':onlyContent}" @click="handleToggleContent">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script>
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;
export default {
  props: {
    ratings: {
      type: Array,
      default() {
        return []
      }
    },
    selectType: {
      type: Number,
      default: ALL
    },
    onlyContent: {
      type: Boolean,
      default: false
    },
    desc: {
      type: Object,
      default() {
        return {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      }
    }
  },
  computed: {
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
    handleSelect(index) {
      this.$emit('select',index)
    },
    handleToggleContent() {
      this.$emit('toggle')
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/mixin'
.rating-type
  display: flex
  margin: 0 18px
  padding: 18px 0
  border-1px(rgba(7,17,27,0.1))
  .type-item
    padding: 8px 12px
    margin-right: 8px
    line-height: 16px
    font-size: 12px
    color: rgb(77,85,93)
    border-radius: 2px
    .number
      font-style: normal
      font-size: 8px
      margin-left:4px
    &.positive
      background: rgba(0,160,220,0.2)
      &.active
        background: rgb(0,160,220)
        color: #fff
    &.nagetive
      background: rgba(77,85,93,0.2)
      &.active
        background: rgb(77,85,93)
        color: #fff
.switch
  padding: 12px 18px
  border-bottom: 1px solid rgba(7,17,27,0.1)
  line-height: 24px
  font-size: 0
  &.on
    .icon-check_circle
      color: #00c850
  .icon-check_circle
    font-size: 24px
    color: rgb(147,153,159)
    vertical-align: middle
  .text
    font-size: 12px
    margin-left: 4px
    vertical-align: middle
</style>