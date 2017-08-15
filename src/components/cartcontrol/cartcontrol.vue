<template>
    <div class="cartcontrol">
        <transition name="move">
            <div v-show="food.count>0" class="cart-decrease icon-remove_circle_outline" @click.stop.prevent="decreaseCart"></div>
        </transition>
        <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
        <div class="cart-add icon-add_circle" @click.stop.prevent="addCart"></div>
    </div>
</template>
<script>
import Vue from 'vue';

export default {
    props: ['food'],
    methods: {
        addCart(event) {
            if (!this.food.count) {
                Vue.set(this.food, 'count', 1);
            } else {
                this.food.count++;
            }
            this.$emit('cartAdd',event.target);
        },
        decreaseCart() {
            if (this.food.count) {
                this.food.count--;
            }
        }
    }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
    .cartcontrol
        font-size:0
        .cart-decrease
            display:inline-block
            padding:6px
            line-height: 24px
            font-size: 24px
            color: rgb(0, 160, 220)
            transform: rotate(180deg)
            transform: translate3d(0, 0, 0)
            &.move-enter-active,&.move-leave-active
                transition: all 0.5s linear
            &.move-enter,&.move-leave-active
                opacity:0
                transform: rotate(0)
                transform: translate3d(24px, 0, 0)
        .cart-count
            display: inline-block
            vertical-align: top
            padding-top: 6px
            font-size:10px
            line-height: 24px
            color: rgb(147, 153, 159)
        .cart-add
            display:inline-block
            padding:6px
            line-height: 24px
            font-size: 24px
            color: rgb(0, 160, 220)
</style>

