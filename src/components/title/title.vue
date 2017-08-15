<template>
  <div class="detail-title">
      <div class="line"></div>
      <div class="text">{{text}}</div>
      <div class="line"></div>
  </div>
</template>
<script>
export default {
  props:['text']
}
</script>
<style lang="stylus" rel="stylesheet/stylus">
    .detail-title
        display:flex
        width:80%
        margin:0 auto
        .line
            flex:1
            position:relative
            top:-7px
            border-bottom:1px solid rgba(255,255,255,.2)
        .text
            padding:0 12px
            font-size:14px
            font-weight:700
</style>


