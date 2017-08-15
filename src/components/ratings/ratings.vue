<template>
  <div class="ratings" ref="ratings">
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
  
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
  
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery-time">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect @ratingTypeSelect="ratingTypeSelect" @contentToggle="contentToggle" :ratings="ratings" :selectType="selectType" :desc="desc" :onlyContent="onlyContent"></ratingselect>
      <div class="rating-wrapper">
        <ul>
          <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in ratings" class="rating-item">
            <div class="avatar">
              <img :src="rating.avatar" alt="">
            </div>
            <div class="content">
              <div class="username">{{rating.username}}</div>
              <div class="delivery-wrapper">
                <star :size="24" :score="rating.score"></star>
                <div v-show="rating.deliveryTime" class="delivery-time">{{rating.deliveryTime}}分钟送达</div>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend">
                <span :class="{'icon-thumb_up':rating.rateType==0,'icon-thumb_down':rating.rateType==1}"></span>
                <span class="item" v-for="item in rating.recommend">
                  {{item}}
                </span>
              </div>
              <div class="time">
                {{rating.rateTime | formatDate}}
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
import star from "@/components/star/star.vue";
import split from "@/components/split/split.vue";
import ratingselect from "@/components/ratingselect/ratingselect.vue";
import BScroll from 'better-scroll';

const ERR_OK = 0;
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;

export default {
  props: ['seller'],
  components: {
    star,
    split,
    ratingselect,
  },
  data() {
    return {
      ratings: [],
      selectType: ALL,
      desc: {
        all: '全部',
        positive: '满意',
        negative: '不满意'
      },
      onlyContent: false,
    }
  },
  created() {
    const self = this;
    self.$axios.get("/api/ratings").then(function (response) {
      if (response.data.errno === ERR_OK) {
        self.ratings = response.data.ratings;
        self.$nextTick(() => {
          self.ratingScroll = new BScroll(self.$refs['ratings'], {
            click: true,
          })
        });
      }
    });
  },
  filters: {
    formatDate: (now) => {
      var now = new Date(now);
      var year = now.getFullYear();
      var month = now.getMonth() + 1;
      var date = now.getDate();
      var hour = now.getHours();
      var minute = now.getMinutes();
      return year + "-" + month + "-" + date + " " + hour + ":" + minute;
    }
  },
  methods:{
    ratingTypeSelect(type){
      this.selectType = type;
      this.$nextTick(()=>{
        this.ratingScroll.refresh();
      });
    },
    needShow(type,text){
      if(!text && this.onlyContent){
        return false;
      }
      if(this.selectType === ALL){
        return true;
      }else{
        return this.selectType === type;
      }
    },
    contentToggle(onlyContent){
      this.onlyContent = onlyContent;
      this.$nextTick(()=>{
        this.ratingScroll.refresh();
      });
    }
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl";

  .ratings
    position: absolute
    top: 174px
    bottom: 0
    left: 0
    width: 100%
    overflow: hidden
    .ratings-content
      .overview
        padding:18px 0
        display:flex
        .overview-left
          flex:0 0 138px
          border-right:1px solid rgba(7,17,27,.1)
          text-align:center
          .score
            font-size:24px
            color:rgb(255,153,0)
            line-height:28px
            margin-bottom:6px
          .title
            font-size:12px
            color:rgb(7,17,27)
            line-height:12px
            margin-bottom:8px
          .rank
            font-size:10px
            color:rgb(147,153,159)
            line-height:10px
            margin-bottom:6px
        .overview-right
          flex:1
          padding:0 24px
          .score-wrapper
            font-size:0
            .title
              display:inline-block
              vertical-align:top
              font-size:12px
              color:rgb(7,17,27)
              line-height:18px
              margin-bottom:8px
            .star
              display:inline-block
              vertical-align:top
              margin:0 12px
            .score
              display:inline-block
              vertical-align:top
              font-size:12px
              color:rgb(255,153,0)
              line-height:18px
          .delivery-wrapper
              font-size:0
              .title
                font-size:12px
                color:rgb(7,17,27)
                line-height:18px
                margin-right:12px
              .delivery-time
                font-size:12px
                color:rgb(147,153,159)
                line-height:18px
      .rating-wrapper
        padding:0 18px
        .rating-item
          padding:18px 0
          display:flex
          border-1px(rgba(7,17,27,.1))
          .avatar
            flex:0 0 40px
            img
              width:28px
              height:28px
              border-radius:50%
              background-size:28px 28px
              background-repeat:no-repeat
          .content
            flex:1
            position:relative
            .username
              font-size:10px
              color:rgb(7,17,27)
              line-height:12px
              margin-bottom:4px
            .delivery-wrapper
              margin-bottom:6px
              font-size: 0
              .star
                display:inline-block
                vertical-align:top
                margin-right:6px
              .delivery-time
                display:inline-block
                vertical-align:top
                font-size:10px
                color:rgb(147,153,159)
                line-height:12px
            .text
              font-size:12px
              color:rgb(7,17,27)
              line-height:18px
              margin-bottom:8px
            .recommend
              font-size:0
              .icon-thumb_up
                font-size:12px
                color:rgb(0,160,220)
                line-height:16px
              .icon-thumb_down
                font-size:12px
                color:rgb(183,187,191)
                line-height:16px
              .item
                display: inline-block
                vertical-align:top
                margin-left:8px
                border:1px solid rgba(7,17,27,.1)
                border-radius:2px
                background-color:rgb(255,255,255)
                padding:0 6px
                font-size:9px
                color:rgb(147,153,159)
                line-height:16px
            .time
              position:absolute
              right:0
              top:0
              font-size:10px
              color:rgb(147,153,159)
              line-height:12px
</style>
