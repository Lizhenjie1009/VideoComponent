<template>
  <div class="video-load">
    <video 
      id="myVideo"
      ref="videoRef"
      class="video-js vjs-default-skin vjs-big-play-centered"
    >
      <source src='#' type="video/mp4">
    </video>
  </div>
</template>

<script>
import Video from 'video.js'
import 'video.js/dist/video-js.css'

export default {
  props: ['chooseSrc', 'chooseIndex', 'videoInfo'],
  data() {
    return {
      // 播放索引
      num: 0,
      // video实例
      myVideo: {},
      // 初始化的第一条视频src
      playVideoSrc: 'http://218.84.186.2:30009/videos/videos/20200416/6815977805591940356.mp4'
    }
  },
  watch: {
    // 监听父组件点击事件
    chooseSrc(val) {
      // 改变索引
      this.num = this.chooseIndex;
      // 改变src
      this.playVideoSrc = val;
      // 更改video的src
      this.$refs.videoRef.setAttribute('src', val)
      // 播放
      this.myVideo.play()
    }
  },
  computed: {
    
  },
  mounted() {
    this.getVideo()
  },
  methods: {
    getVideo() {
      // video实例
      this.myVideo = Video('myVideo', {
        controls: true,
        preload: 'metadata',
        // autoplay: true,
        poster: "http://vjs.zencdn.net/v/oceans.png"
      }, () => {
        // video初始化完成，为video赋值
        this.$refs.videoRef.setAttribute('src', this.playVideoSrc)


        // 播放事件
        this.myVideo.on('play', (e) => {
          // 向父组件发送播放的src
          this.$emit('playVideo', this.playVideoSrc)
        })
       

        // 结束事件
        this.myVideo.on('ended', () => {
          // 播放下一条视频
          this.num++;
          // 边界判断
          if (this.num > this.videoInfo.length - 1) this.num = 0;
          // 结束改变播放视频的src
          this.playVideoSrc = this.videoInfo[this.num].saveUrl;
          // 向父组件传递下一条视频信息
          this.$emit('endPlayVideo', {
            // 下一条视频src
            src: this.videoInfo[this.num].saveUrl, // 可以优化掉
            // 下一条视频的索引
            index: this.num,
            // 当前播放结束
            end: true
          })
          // 改变video的src
          this.$refs.videoRef.setAttribute('src', this.videoInfo[this.num].saveUrl)
          // 下一条视频播放
          this.myVideo.play()
        })
      })
    }
  },
}
</script>

<style lang="scss" scoped>
  #myVideo {
    width: 100%;
    height: 100%;
    object-fit: fill;
    // margin-left: 15%;
  }
  
  // 暂停显示
  /deep/.vjs-paused .vjs-big-play-button,
  .vjs-paused.vjs-has-started .vjs-big-play-button {
    display: block;
  } 

// 变圆
/deep/.video-js .vjs-big-play-button{
    font-size: 2.5em;
    line-height: 2.3em;
    height: 2.5em;
    width: 2.5em;
    -webkit-border-radius: 2.5em;
    -moz-border-radius: 2.5em;
    border-radius: 2.5em;
    background-color: #73859f;
    background-color: rgba(115,133,159,.5);
    border-width: 0.15em;
    margin-top: -1.25em;
    margin-left: -1.25em;
}
/* 中间的播放箭头 */
/deep/.vjs-big-play-button .vjs-icon-placeholder {
    font-size: 1.63em;
}
/* 加载圆圈 */
/deep/.vjs-loading-spinner {
    font-size: 2.5em;
    width: 2em;
    height: 2em;
    border-radius: 1em;
    margin-top: -1em;
    margin-left: -1.5em;
}
</style>