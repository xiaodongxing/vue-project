<template>
    <div class="header">
      	<div class="content-wrapper">
      		<div class="avatar">
      			<img width="64" height="64" :src="seller.avatar">
      		</div>
      		<div class="content">
      			<div class="title">
      				<span class="brand"></span>
      				<span class="name">{{seller.name}}</span>
      			</div>
      			<div class="description">
      				{{seller.description}}/{{seller.deliveryTime}}分钟送达
      			</div>
      			<div v-if="seller.supports" class="support">
      				<span class="icon" :class="classMap[seller.supports[0].type]"></span>
      				<span class="text">{{seller.supports[0].description}}</span>

      			</div>
      		</div>
      		<div class="support-count" v-if="seller.supports" @click="handleDetailShow">
      			<span class="count">{{seller.supports.length}}个</span>
      			<i class="icon-keyboard_arrow_right"></i>
      		</div>
      	</div>
      	<div class="bullentin-wrapper" @click="handleDetailShow">
      		<span class="bullentin-title"></span><span class="bullentin-text">{{seller.bulletin}}</span>
      		<i class="icon-keyboard_arrow_right"></i>
      	</div>
      	<div class="background">
      		<img :src="seller.avatar" width="100%" height="100%">
      	</div>
      	<fade-animation>
      		<div v-show="detailShow" class="detail">
	      		<div class="detail-wrapper">
	      				<h1 class="name">{{seller.name}}</h1>
	      				<div class="star-wrapper">
	      					<star :size="48" :score="seller.score"></star>
	      				</div>
	      				<div class="detail-content">
	      					<div class="title">
		      					<div class="line"></div>
		      					<div class="text">优惠信息</div>
		      					<div class="line"></div>
		      				</div>
		      				<ul v-if="seller.supports" class="supports">
		      					<li class="support-item" v-for="(item, index) in seller.supports">
		      						<span class="icon" :class="classMap[seller.supports[index].type]"></span>
		      						<span class="text">{{seller.supports[index].description}}</span>
		      					</li>
		      				</ul>
	      				</div>
	      				<div class="detail-content">
	      					<div class="title">
		      					<div class="line"></div>
		      					<div class="text">商家公告</div>
		      					<div class="line"></div>
		      				</div>
		      				<div class="bulletin">
		      					<p class="content">{{seller.bulletin}}</p>
		      				</div>
	      				</div>
	      		</div>
	      		<div class="detail-close">
	      			<i class="icon-close" @click="handleDetailClose"></i>
	      		</div>
	      	</div>
      	</fade-animation>
      	
    </div>
</template>

<script>
import star from 'components/star/star'
import FadeAnimation from 'components/animation/fade'
export default{
	props: {
		seller: {
			type: Object
		}
	},
	components: {
		star,
		FadeAnimation
	},
	data() {
		return {
			detailShow: false
		}
	},
	created() {
		this.classMap = ['decrease','discount','special','invoice','guarantee']
	},
	methods: {
		handleDetailShow() {
			this.detailShow = true
		},
		handleDetailClose() {
			this.detailShow = false
		}
	}
}
</script>
<style lang="stylus">
@import '~common/stylus/mixin.styl'
	.header
		position: relative
		overflow: hidden
		color: #fff
		background: rgba(7,17,27,0.5)
		.content-wrapper
			position: relative
			padding: 24px 12px 18px 24px
			font-size: 0
			.avatar
				display: inline-block
				vertical-align: top
			.content
				display: inline-block
				margin-left: 16px
				.title
					margin: 2px 0 8px 0
					.brand
						display: inline-block
						width: 30px
						height: 18px
						vertical-align: top
						bg-image('brand')
						background-size: 30px 18px
						background-repeat: no-repeat
					.name
						margin-left: 6px
						font-size: 16px
						line-height: 18px
						font-weight: bold
				.description
					margin-bottom: 10px
					line-height: 12px
					font-size: 12px
				.support
					.icon
						display: inline-block
						vertical-align: top
						width: 12px
						height: 12px
						margin-right: 4px
						background-size: 12px 12px
						background-repeat: no-repeat
						&.decrease
							bg-image('decrease_1')
						&.discount
							bg-image('discount_1')
						&.guarantee
							bg-image('guarantee_1')
						&.invoice
							bg-image('invoice_1')
						&.special
							bg-image('special_1')
					.text
						ling-height:12px
						font-size: 10px
			.support-count
				position: absolute
				bottom: 18px
				right: 12px
				padding: 0 8px
				height: 24px
				line-height: 24px
				border-radius: 14px
				background-color: rgba(0,0,0,0.2)		
				.count
					font-size: 10px
					margin-right: 2px
					vertical-align: top
				.icon-keyboard_arrow_right
					line-height:24px
					font-size: 10px
		.bullentin-wrapper
			position: relative
			padding: 0 22px 0 12px
			height: 28px
			line-height: 28px
			font-size: 10px
			background-color: rgba(7,17,27,0.2)
			ellipsis()
			.bullentin-title
				display: inline-block
				vertical-align: top
				margin-top: 8px
				width: 22px
				height: 12px
				bg-image('bulletin')
				background-size: 22px 12px
				background-repeat: no-repeat
			.bullentin-text
				vertical-align: top
				margin: 0 4px				
			.icon-keyboard_arrow_right
				position: absolute
				right: 12px
				top: 8px
		.background
			position: absolute
			top: 0
			left: 0
			width: 100%
			height: 100%
			z-index: -1
			overflow: hidden
			filter: blur(10px)
		.detail
			position: fixed
			top: 0
			z-index: 999
			width: 100%
			height: 100%
			overflow: auto
			background-color: rgba(7,17,27,0.8)
			backdrop-filter: blur(10px);
			padding: 0 36px
			box-sizing: border-box
			.detail-wrapper
				min-height: 100%
				padding-top: 64px
				padding-bottom: 64px
				box-sizing: border-box
				.name
					line-height: 16px
					text-align: center
					font-size: 16px
					font-weight: 700
				.star-wrapper
					margin-top: 18px
					padding: 2px 0.2
					text-align: center
				.detail-content
					overflow: hidden
					font-size: 12px
					.title
						display: flex
						margin: 28px auto 24px
						.line
							flex: 1
							position: relative
							top: -6px
							border-bottom: 1px solid rgba(255,255,255,0.2)
						.text
							padding: 0 12px
							font-size: 14px
							font-weight: 700
					.supports
						padding: 0 12px
						.support-item
							margin-bottom: 12px
							font-size: 0
							&:last-child
								margin-bottom: 0
							.icon
								display: inline-block
								vertical-align: top
								width: 16px
								height: 16px
								margin-right: 6px
								background-size: 16px 16px
								background-repeat: no-repeat
								&.decrease
									bg-image('decrease_1')
								&.discount
									bg-image('discount_1')
								&.guarantee
									bg-image('guarantee_1')
								&.invoice
									bg-image('invoice_1')
								&.special
									bg-image('special_1')
							.text
								font-size: 12px
								line-height: 16px
					.bulletin
						padding: 0 12px
						.content
							line-height: 24px
			.detail-close
				position: relative
				width: 32px
				height: 32px
				margin: -64px auto  0
				clear: both
				font-size: 32px
	.fade-enter, .fade-leave-to
		opacity: 0
	.fade-enter-active, .fade-leave-active
		transition: opacity 1s
</style>

