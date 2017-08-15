<template>
    <div class="ratingselect-wrapper">
        <div class="rating-type border-1px" v-if="ratings">
            <span @click="select(2)" class="block positive" :class="{'active':selectedType===2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
            <span @click="select(0)" class="block positive" :class="{'active':selectedType===0}">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
            <span @click="select(1)" class="block negative" :class="{'active':selectedType===1}">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
        </div>
        <div  @click="toggleContent" class="switch" :class="{'on':onlyLookContent}">
            <span class="icon-check_circle" ></span>
            <span class="text">只看有内容的评价</span>
        </div>
    </div>
</template>
<script>

const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;

export default {
    props: ['ratings', 'selectType', 'desc', 'onlyContent'],
    methods:{
        select(type){
            this.selectedType = type;

            this.$emit("ratingTypeSelect",type);
        },
        toggleContent(){
            this.onlyLookContent = !this.onlyLookContent;

            this.$emit("contentToggle",this.onlyLookContent);
        }
    },
    computed:{
        positives(){
            return this.ratings.filter((rating)=>{
                return rating.rateType === POSITIVE;
            })
        },
        negatives(){
            return this.ratings.filter((rating)=>{
                return rating.rateType === NEGATIVE;
            });
        }
    },
    data(){
        return{
            selectedType:ALL,
            onlyLookContent:false,
        }
    },
    created(){
        this.selectedType = this.selectType;
        this.onlyLookContent = this.onlyContent;
    }
}
</script>
<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl"

    .ratingselect-wrapper
        padding-top:18px
        .rating-type
            margin:0 18px
            padding-bottom:18px
            border-1px(rgba(7,17,27,.1))
            font-size:0
            .block
                display:inline-block
                font-size:12px
                color: rgb(77, 85, 93)
                line-height:16px
                padding:8px 12px
                margin-right:8px
                .count
                    margin-left: 2px
                    font-size: 8px
                &.active
                    color:#fff
                &:last-child
                    margin-right:0
                &.positive
                    background: rgba(0, 160, 220, 0.2)
                    &.active
                        background: rgb(0, 160, 220)
                &.negative
                    background: rgba(77, 85, 93, 0.2)
                    &.active
                        background: rgb(77, 85, 93)
        .switch
            padding:12px 18px
            border-1px(rgba(7,17,27,.1))
            &.on
                .icon-check_circle
                    color:#00c850
            .icon-check_circle
                display:inline-block
                vertical-align:top
                font-size:24px
                color:rgb(147,153,159)
                line-height:24px
                margin-right:4px
            .text
                font-size:12px
                color:rgb(147,153,159)
                line-height:24px
                

</style>


