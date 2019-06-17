<template>
  <div class="recommend">

      <scroll ref="srcoll" class="recommend-content" :data="discList">
        <div>
          <div v-if="recommends.length" class="slider-wrapper" ref="sliderWrapper">
            <slider>
              <div v-for="item in recommends">
                <a :href="item.linkUrl">
                  <img @onload="loadImage" class="needsclick"  :src="item.picUrl">
                </a>
              </div>
            </slider>
          </div>
         <div class="recommend-list">
          <h1 class="list-title">热门歌单推荐</h1>
          <ul>
            <li v-for="item in discList" class="item">
              <div class="icon">
                <img v-lazy="item.imgurl" width="60" height="60" alt="">
              </div>
              <div class="text">
                <h2 class="name" v-html="item.creator.name"></h2>
                <p class="desc" v-html="item.dissname"></p>
              </div>
            </li>
          </ul>
         </div>
        </div>
        <div class="loading-container" v-show="!discList.length">
          <loading></loading>
        </div>
      </scroll>


  </div>
</template>

<script>
import Slider from 'base/slider/slider'
import Loading from 'base/loading/loading'
import {getRecommend, getDiscList} from 'api/recommend'
import {ERR_OK} from 'api/config'
import Scroll from 'base/scroll/scroll'
export default {
  data() {
    return {
      recommends: [],
      discList: []
    }
  },
  created() {
    // 给一个检查状态只执行一次
    this.checkLoaded = false
    // setTimeout(() => {
    //   this._getRecommend()
    // }, 2000)
    this._getRecommend()
    this._getDiscList()
  },
  methods: {
    _getRecommend() {
      getRecommend().then((res) => {
        if (res.code === ERR_OK) {
          this.recommends = res.data.slider
          // console.log(this.recommends)
        }
      })
    },
    _getDiscList() {
      getDiscList().then((res) => {
        if (res.code === ERR_OK) {
          this.discList = res.data.list
        }
      })
    },
    loadImage() {
      // 因为图片是多张,如果多次刷新是浪费,所以执行这个变量，有一个图片就能够撑开高度了
      if (!this.checkLoaded) {
        this.$refs.scroll.refresh()
        this.checkLoaded = true
      }
    }
  },
  components: {
    Slider,
    Scroll,
    Loading
  }
}
</script>

<style lang="stylus" scoped>
@import "~common/stylus/variable"
  .recommend
    position: fixed
    width: 100%
    top: 88px
    bottom: 0
    .recommend-content
      height: 100%
      overflow: hidden
      .slider-wrapper
        position: relative
        width: 100%
        overflow: hidden
      .recommend-list
        .list-title
          height: 65px
          line-height: 65px
          text-align: center
          font-size: $font-size-medium
          color: $color-theme
        .item
          display: flex
          box-sizing: border-box
          align-items: center
          padding: 0 20px 20px 20px
          .icon
            flex: 0 0 60px
            width: 60px
            padding-right: 20px
          .text
            display: flex
            flex-direction: column
            justify-content: center
            flex: 1
            line-height: 20px
            overflow: hidden
            font-size: $font-size-medium
            .name
              margin-bottom: 10px
              color: $color-text
            .desc
              color: $color-text-d
      .loading-container
        position: absolute
        width: 100%
        top: 50%
        transform: translateY(-50%)
</style>
