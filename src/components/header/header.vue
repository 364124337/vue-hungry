<template>
    <div class="header">
        <div class="content-wrapper">
            <div class="avatar">
                <img :src="seller.avatar" width="64" height="64" alt="">
            </div>
            <div class="content">
                <div class="title">
                    <span class="brand"></span>
                    <span class="name">{{seller.name}}</span>
                </div>
                <div class="description">
                    {{seller.description}} / {{seller.deliveryTime}}分钟送达
                </div>
                <div class="support" v-if="seller.supports">
                    <span class="icon" :class="classMap[seller.supports[0].type]"></span>
                    <span class="text">{{seller.supports[0].description}}</span>
                </div>
            </div>
            <div v-if="seller.supports" @click="showDetail" class="support-count">
                <span class="count">{{seller.supports.length}}个</span>
                <i class="icon-keyboard_arrow_right"></i>
            </div>
        </div>
        <div class="bulletin-wrapper" @click="showDetail">
            <span class="bulletin-title"></span>
            <!--
                      -->
            <span class="bulletin-text">{{seller.bulletin}}</span>
            <i class="icon-keyboard_arrow_right"></i>
        </div>
        <div class="background">
            <img :src="seller.avatar" width="100%" height="100%" alt="">
        </div>
        <transition name="fade">
            <div v-show="detailShow" class="detail">
                <!--添加clearfix是防止margin塌陷问题-->
                <div class="detail-wrapper clearfix">
                    <div class="detail-main">
                        <h1 class="name">{{seller.name}}</h1>
                        <div class="star-wrapper">
                            <star :size="48" :score="seller.score"></star>
                        </div>
                        <div class="title-wrapper">
                            <v-title :text="'优惠信息'"></v-title>
                        </div>
                        <ul class="support-wrapper" v-if="seller.supports">
                            <li class="support" v-for="support in seller.supports">
                                <span class="icon" :class="classMap[support.type]"></span>
                                <span class="text">{{support.description}}</span>
                            </li>
                        </ul>
                        <div class="title-wrapper">
                            <v-title :text="'商家公告'"></v-title>
                        </div>
                        <div class="bulletin-content">
                            <p>{{seller.bulletin}}</p>
                        </div>
                    </div>
                </div>
                <div class="detail-close">
                    <i class="icon-close" @click="showDetail"></i>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
import star from '@/components/star/star.vue';
import vTitle from '@/components/title/title.vue';

export default {
    props: ['seller'],
    created() {
        this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    data() {
        return {
            detailShow: false
        }
    },
    methods: {
        showDetail() {
            this.detailShow = !this.detailShow;
        }
    },
    components: {
        star,
        vTitle,
    }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import '../../common/stylus/mixin.styl'

    .header
        overflow:hidden
        position:relative
        color:#fff
        background:rgba(7,17,27,.5)
        .content-wrapper
            position:relative
            padding:24px 12px 18px 24px
            font-size:0
            .support-count
                position:absolute
                right:12px
                bottom:18px
                line-height:24px
                height:24px
                background-color:rgba(0,0,0,0.2)
                padding:0 8px
                border-radius:14px
                text-align:center
                .count
                    font-size:10px
                    vertical-align:top
                .icon-keyboard_arrow_right
                    line-height:24px
                    margin-left:2px
                    font-size:10px
            .avatar
                display:inline-block
                margin-right:16px
                vertical-align:top
                img
                    border-radius:2px
            .content
                display:inline-block
                .title
                    margin:2px 0 8px 0
                    .brand
                        display:inline-block
                        width:30px
                        height:18px
                        bg-image('brand')
                        background-size:30px 18px
                        background-repeat:no-repeat
                        vertical-align:top
                    .name
                        margin-left:6px
                        font-size:16px
                        font-weight:bold
                        line-height:18px
                .description
                    font-size:12px
                    line-height:12px
                    margin-bottom:10px
                .support
                    margin-bottom:2px
                    .icon
                        display:inline-block
                        width:12px
                        height:12px
                        background-size:12px 12px
                        background-repeat:no-repeat
                        vertical-align:top
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
                        font-size:14px
                        line-height:12px
                        margin-left:4px
        .bulletin-wrapper
            position:relative
            padding:0 22px 0 12px
            height:28px
            line-height:28px
            overflow: hidden;
            text-overflow:ellipsis;
            white-space: nowrap;
            background:rgba(7,17,27,.2)
            .bulletin-title
                display:inline-block
                vertical-align:middle
                width:22px
                height:12px
                background-size:22px 12px
                background-repeat:no-repeat
                bg-image('bulletin');
            .bulletin-text
                font-size:10px
                margin:0 4px
            .icon-keyboard_arrow_right
                position:absolute
                right:12px
                top:12px
                font-size:10px
        .background
            position:absolute
            top:0
            left:0
            width:100%
            height:100%
            z-index:-1
            filter:blur(10px)
        .detail
            position:fixed
            top:0
            left:0
            z-index:100
            width:100%
            height:100%
            overflow:auto
            background:rgba(7,17,27,.8)
            &.fade-enter-active,&.fade-leave-active
                transition:1s all ease
            &.fade-enter,&.fade-leave-active
                opacity:0
            .detail-wrapper
                min-height:100%
                width:100%
                .detail-main
                    margin-top:64px
                    padding-bottom:64px
                    .name
                        font-size:16px
                        font-weight:700
                        line-height:16px
                        text-align:center
                        margin-bottom:16px
                    .star-wrapper
                        text-align:center
                        margin-bottom:28px
                    .title-wrapper
                        margin-bottom:24px
                    .support-wrapper
                        width:80%
                        margin: 0 auto 28px auto
                        .support
                            padding:0 12px
                            margin-bottom:12px
                            font-size:0
                            &:last-child
                                margin-bottom:0
                            .icon
                                display:inline-block
                                width:16px
                                height:16px
                                background-size:16px 16px
                                background-repeat:no-repeat
                                vertical-align:top
                                &.decrease
                                    bg-image('decrease_2')
                                &.discount
                                    bg-image('discount_2')
                                &.guarantee
                                    bg-image('guarantee_2')
                                &.invoice
                                    bg-image('invoice_2')
                                &.special
                                    bg-image('special_2')
                            .text
                                font-size:12px
                                line-height:16px
                                margin-left:6px
                    .bulletin-content
                        padding:0 12px
                        p
                            font-size:12px
                            line-height:24px
                            width:80%
                            margin 0 auto
            .detail-close
                position:relative
                width:32px
                height:32px
                margin:-64px auto 0 auto
                font-size:32px
                clear:both
</style>
