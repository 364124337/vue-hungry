<template>
    <div>
        <div class="goods">
            <div class="menu-wrapper" ref="menu-wrapper">
                <ul>
                    <li v-for="(item,index) in goods" class="menu-item" :class="{'current':index===currentIndex}" @click.stop.prevent="selectMenu(index)">
                        <span class="text border-1px">
                            <span class="icon" v-if="item.type !== -1" :class="classMap[item.type]"></span>
                            {{item.name}}
                        </span>
                    </li>
                </ul>
            </div>
            <div class="foods-wrapper" ref="foods-wrapper">
                <ul>
                    <li v-for="item in goods" class="food-list food-list-hook">
                        <h1 class="title">{{item.name}}</h1>
                        <ul>
                            <li @click="selectFood(food)" v-for="food in item.foods" class="food-item border-1px">
                                <div class="icon">
                                    <img width="57" height="57" :src="food.icon" alt="">
                                </div>
                                <div class="content">
                                    <h2 class="name">{{food.name}}</h2>
                                    <p class="desc">{{food.description}}</p>
                                    <div class="extra">
                                        <span class="count">月售{{food.sellCount}}份</span>
                                        <!--
                                                    -->
                                        <span>好评率{{food.rating}}%</span>
                                    </div>
                                    <div class="price">
                                        <span class="now">￥{{food.price}}</span>
                                        <!--
                                                    -->
                                        <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                                    </div>
                                    <div class="cartcontrol-wrapper">
                                        <cartcontrol @cartAdd="cartAdd" :food="food"></cartcontrol>
                                    </div>
                                </div>
                            </li>
                        </ul>
                    </li>
                </ul>
            </div>
            <shopcart  @cartAdd="cartAdd" ref="shopcart" :delivery-price="seller.deliveryPrice" :select-foods="selectFoods" :min-price="seller.minPrice"></shopcart>
        </div>
        <food  @cartAdd="cartAdd" ref="food" :food="selectedFood"></food>
    </div>
</template>
<script>

import BScroll from 'better-scroll';
import cartcontrol from '../../components/cartcontrol/cartcontrol';
import shopcart from '../../components/shopcart/shopcart';
import food from '../../components/food/food';


const ERR_OK = 0;

export default {
    props: ['seller'],
    data() {
        return {
            goods: [],
            scrollY: 0,
            listHeight: [],
            selectedFood: {},
        }
    },
    created() {
        this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];

        const self = this;
        this.$axios.get('/api/goods').then(function (response) {
            if (response.data.errno === ERR_OK) {
                self.goods = response.data.goods;
                self.$nextTick(() => {
                    self._initScroll();
                    self._calculateHeight();
                })
            }
        })
    },
    methods: {
        selectMenu(index) {
            let foodList = this.$refs['foods-wrapper'].getElementsByClassName('food-list-hook');
            let el = foodList[index];
            this.foodsScroll.scrollToElement(el, 300);
        },
        _initScroll() {
            this.meunScroll = new BScroll(this.$refs['menu-wrapper'], {
                click: true,
                probeType: 3,
            });

            this.foodsScroll = new BScroll(this.$refs['foods-wrapper'], {
                click: true,
                probeType: 3,
            });

            this.foodsScroll.on('scroll', (pos) => {
                this.scrollY = Math.abs(Math.round(pos.y));
            })
        },
        _calculateHeight() {
            let foodList = this.$refs['foods-wrapper'].getElementsByClassName('food-list-hook');
            let height = 0;
            this.listHeight.push(height);
            for (let i = 0; i < foodList.length; i++) {
                let item = foodList[i];
                height += item.clientHeight;
                this.listHeight.push(height);
            }
        },
        cartAdd(target) {
            this._drop(target);
        },
        _drop(target) {
            // 体验优化,异步执行下落动画
            this.$nextTick(() => {
                this.$refs['shopcart'].drop(target);
            });
        },
        selectFood(food) {
            this.selectedFood = food;

            this.$refs['food'].show();
        }
    },
    computed: {
        currentIndex() {
            for (let i = 0; i < this.listHeight.length; i++) {
                let height1 = this.listHeight[i];
                let height2 = this.listHeight[i + 1];
                if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
                    return i;
                }
            }
            return 0;
        },
        selectFoods() {
            let foods = [];
            this.goods.forEach((good) => {
                good.foods.forEach((food) => {
                    if (food.count) {
                        foods.push(food);
                    }
                })
            })
            return foods;
        }
    },
    components: {
        cartcontrol,
        shopcart,
        food,
    }
}
</script>
<style lang="stylus" rel="stylesheet/stylus">
@import '../../common/stylus/mixin.styl';

    .goods
        display:flex
        position:absolute
        top:174px
        bottom:46px
        width:100%
        overflow:hidden
        .menu-wrapper
            flex:0 0 80px
            width:80px
            background-color:#f3f5f7
            .menu-item
                display:table
                height:54px
                width:56px
                padding:0 12px
                font-size:0
                line-height:14px
                &.current
                    position:relative
                    z-index:10
                    background:#fff
                    margin-top:-1px
                    .text
                        border-none()
                .text
                    .icon
                        display:inline-block
                        width:16px
                        height:16px
                        background-size:16px 16px
                        background-repeat:no-repeat
                        vertical-align:top
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
                    display:table-cell
                    width:56px
                    vertical-align:middle
                    font-size:12px
                    border-1px(rgba(7,17,27,.1))
        .foods-wrapper
            flex:1
            .food-list
                .title
                    border-left:2px solid #d9dde1
                    height:26px
                    background:#f3f5f7
                    line-height:26px
                    color:rgb(147,153,159)
                    font-size:12px
                    padding-left:14px
                    font-weight:700
                .food-item
                    display:flex
                    margin:18px 18px 0 18px
                    padding-bottom:18px
                    border-1px(rgba(7,17,27,.1))
                    &:last-child
                        border-none()
                    .icon
                        flex:0 0 57px
                    .content
                        flex:1
                        margin-left:10px
                        .name
                            font-size:14px
                            color:rgb(7,17,27)
                            line-height:14px
                            font-weight:700
                            margin:2px 0 8px
                        .desc
                            font-size:10px
                            color:rgb(147,153,159)
                            line-height:10px
                            margin-bottom:8px
                        .extra
                            font-size:10px
                            color:rgb(147,153,159)
                            line-height:10px
                            .count
                                margin-right:12px
                        .price
                            color:rgb(147,153,159)
                            line-height:24px
                            .now
                                font-weight:700
                                color:rgb(240,20,20)
                                font-size:14px
                                margin-right:8px
                            .old
                                text-decoration: line-through
                                font-weight:700
                                font-size:10px
                        .cartcontrol-wrapper
                            position:absolute
                            right:0
                            bottom:12px

</style>
