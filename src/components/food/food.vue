<template>
    <transition name="move">
        <div ref="food" v-show="showFlag" class="food">
            <div class="food-content">
                <div class="image-header">
                    <img :src="food.image" alt="">
                    <i class="icon-arrow_lift" @click.stop.prevent="hide"></i>
                </div>
                <div class="content">
                    <div class="name">{{food.name}}</div>
                    <div class="extra">
                        <span class="count">月售{{food.sellCount}}份</span><!--
                        --><span>好评率{{food.rating}}%</span>
                    </div>
                    <div class="price">
                        ￥<span class="now">{{food.price}}</span><!--
                        --><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                    </div>
                    <div class="cartcontrol-wrapper">
                        <cartcontrol  @cartAdd="cartAdd" :food="food"></cartcontrol>
                    </div>
                    <transition name="fade">
                        <div @click.stop.prevent="addFirst" class="buy" v-show="!food.count || food.count===0">加入购物车</div>
                    </transition>
                </div>
                <split v-show="food.info"></split>
                <div class="introduce-wrapper" v-show="food.info">
                    <h1 class="title">
                        商品介绍
                    </h1>
                    <p class="info">
                        {{food.info}}
                    </p>
                </div>
                <split></split>
                <div class="rating">
                    <h1 class="title">商品评价</h1>
                    <ratingselect @contentToggle="contentToggle" @ratingTypeSelect="ratingTypeSelect" :ratings="food.ratings" :selectType="selectType" :desc="desc" :onlyContent="onlyContent"></ratingselect>
                    <div class="rating-wrapper">
                        <ul>
                            <li v-show="needShow(rating.rateType,rating.text)" class="rating-item border-1px" v-for="rating in food.ratings">
                                <div class="time">{{rating.rateTime | formatDate}}</div>
                                <div class="user">
                                    <span class="name">{{rating.username}}</span>
                                    <img class="avatar" width="12" height="12" :src="rating.avatar" alt="">
                                </div>
                                <p class="text">
                                    <span :class="{'icon-thumb_up':rating.rateType === 0,'icon-thumb_down':rating.rateType===1}"></span>{{rating.text}}
                                </p>
                            </li>
                        </ul>
                        <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
                    </div>
                </div>
            </div>
        </div>
    </transition>
</template>

<script>
import BScroll from 'better-scroll';
import Vue from 'vue';
import cartcontrol from '../../components/cartcontrol/cartcontrol.vue';
import split from '../../components/split/split.vue';
import ratingselect from '../../components/ratingselect/ratingselect.vue';

const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;

export default {
    props: ['food'],
    data() {
        return {
            showFlag: false,
            selectType:ALL,
            desc:{
                all: '全部',
                positive: '推荐',
                negative: '吐槽'
            },
            onlyContent:false,
        }
    },
    methods: {
        show() {
            this.showFlag = true;

            this.$nextTick(()=>{
                this._initScroll();
            });
        },
        hide() {
            this.showFlag = false;
        },
        _initScroll(){
            if(!this.foodScroll){
                this.foodScroll = new BScroll(this.$refs['food'],{
                    click:true,
                })
            }else{
                this.foodScroll.refresh();
            }
        },
        addFirst(event){
            this.cartAdd();
            Vue.set(this.food,'count',1);
        },
        cartAdd(){
            this.$emit('cartAdd',event.target);
        },
        ratingTypeSelect(type){
            this.selectType = type;
            this.$nextTick(() => {
                this.foodScroll.refresh();
            });
        },
        needShow(type,text){
            if(this.onlyContent && !text){
                return false;
            }
            if(this.selectType === ALL){
                return true;
            }else{
                return type === this.selectType;
            }
        },
        contentToggle(onlyContent){
            this.onlyContent = onlyContent;
            this.$nextTick(() => {
                this.foodScroll.refresh();
            });
        }
    },
    components:{
        cartcontrol,
        split,
        ratingselect,
    },
    filters:{
        formatDate:(now)=> { 
            var now = new Date(now);
            var year=now.getFullYear(); 
            var month=now.getMonth()+1; 
            var date=now.getDate(); 
            var hour=now.getHours(); 
            var minute=now.getMinutes(); 
            return year+"-"+month+"-"+date+" "+hour+":"+minute; 
        } 
    }
}
</script>

<style lang="stylus" rel="stylesheet/stylus"> 
@import "../../common/stylus/mixin.styl"

    .food
        position:fixed
        left:0
        top:0
        bottom:48px
        z-index:30
        width:100%
        background:#fff
        transform:translateX(0)
        &.move-enter-active,&.move-leave-active
            transition:all 0.2s linear
        &.move-enter,&.move-leave-active
            transform:translateX(100%)
        .image-header
            width:100%
            position:relative
            padding-top:100%
            height:0
            img
                position:absolute
                left:0
                top:0
                width:100%
                height:100%
            .icon-arrow_lift
                position:absolute
                left:10px
                top:10px
                width:25px
                height:25px
                color:#fff
        .content
            padding:18px
            position:relative
            .name
                font-size:14px
                font-weight:700
                color:rgb(7,17,27)
                line-height:14px
                margin-bottom:8px
            .extra
                font-size:10px
                color:rgb(147,153,159)
                line-height:10px
                margin-bottom:18px
                .count
                    margin-right:12px
            .price
                color:rgb(240,20,20)
                line-height:24px
                .now
                    font-weight:700
                    font-size:24px
                    margin-right:8px
                .old
                    font-weight:700
                    color:rgb(147,153,159)
                    font-size:10px
                    text-decoration: line-through
            .cartcontrol-wrapper
                position:absolute
                right:18px
                bottom:12px
            .buy
                position:absolute
                right:18px
                bottom:18px
                padding:6px 12px
                font-size:10px
                line-height:12px
                color:rgb(255,255,255)
                border-radius:12px
                background-color:rgb(0,160,220)
                width:62px
                text-align:center
                &.fade-enter-active,&.fade-leave-active
                    transition:all .2s
                &.fade-enter,&.fade-leave-active
                    opacity:0
        .introduce-wrapper
            padding:18px
            .title
                font-size:14px
                font-weight:700
                color:rgb(7,17,27)
                line-height:14px
            .info
                margin:6px 8px 0 8px
                font-size:12px
                color:rgb(77,85,93)
                line-height:24px
        .rating 
            padding-top:18px
            .title
                font-size:14px
                font-weight:700
                color:rgb(7,17,27)
                line-height:14px 
                margin-left:18px
            .rating-wrapper
                padding:0 18px
                .rating-item
                    padding:16px 0
                    position:relative
                    border-1px(rgba(7,17,27,.1))
                    .time
                        font-size:10px
                        color:rgb(147,153,159)
                        line-height:12px
                        margin-bottom:6px
                    .user
                        font-size:0
                        position:absolute
                        right:0
                        top:18px
                        .name
                            display:inline-block
                            vertical-align:top
                            font-size:10px
                            color:rgb(147,153,159)
                            line-height:12px
                            margin-right:6px
                        .avatar
                            border-radius:50%
                            width:12px
                            height:12px
                            background-repeat:no-repeat
                            background-size:12px 12px
                    .text
                        font-size:12px
                        color:rgb(7,17,27)
                        line-height:16px
                        .icon-thumb_up, .icon-thumb_down
                            margin-right: 4px
                            line-height: 16px
                            font-size: 12px
                        .icon-thumb_up
                            color: rgb(0, 160, 220)
                        .icon-thumb_down
                            color: rgb(147, 153, 159)
                .no-rating
                    padding: 16px 0
                    font-size: 12px
                    color: rgb(147, 153, 159)
                        
                    
</style>


