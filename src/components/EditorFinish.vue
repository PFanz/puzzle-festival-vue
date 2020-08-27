<template>
  <div class="wrap-finish">
    <div class="finish-bg-header"></div>
    <div class="finish-bg-footer"></div>

    <div class="wrap-pre-img">
      <canvas
        v-if="!previewRemote"
        class="wrap-canvas"
        width="1125"
        height="1680"
        ref="finishCanvas"
      ></canvas>

      <img v-else :src="fetchUrl" alt class="remote-preview" />
    </div>
    <div style="position: relative; text-align: center; font-size: 0;">
      <div>
        <a href="javascript: void(0)" class="btn-publish" @click="postFeed"
          >发布动态</a
        >
      </div>
      <div class="wrap-sub-btns">
        <a href="javascript: void(0)" class="finish-sub-btn" @click="reedit"
          >重新制作</a
        >
        <a href="javascript: void(0)" class="finish-sub-btn" @click="sharePic"
          >分享</a
        >
      </div>
    </div>
  </div>
</template>

<script>
import CodeImg from '../assets/images/img_qcode.png'

export default {
  props: {
    imgUrl: {
      type: String,
      default: ''
    },
    previewRemote: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      fetchUrl: '',
      uploadShow: false,
      codeImage: CodeImg,
      errorFlag: false
    }
  },
  created () {},
  mounted () {
    // const currSubject = 'JIZAO'

    if (this.previewRemote) {
      this.fetchUrl = this.imgUrl
    } else {
      const canvas = this.$refs.finishCanvas
      const ctx = canvas.getContext('2d')

      // 白色背景
      ctx.fillStyle = '#fff'
      ctx.fillRect(0, 0, 1125, 1680)

      const img = new Image()
      img.src = this.imgUrl
      const img2 = new Image()
      img2.src = this.codeImage

      const imgPromise = new Promise(resolve => {
        img.onload = function () {
          ctx.drawImage(img, 60, 60, 1002, 1336)
          ctx.save()
          resolve()
        }
      })

      const imgPromise2 = new Promise(resolve => {
        img2.onload = function () {
          ctx.drawImage(img2, 60, 60 + 1336 + 42, 876, 192)
          ctx.save()
          resolve()
        }
      })

      Promise.all([imgPromise, imgPromise2]).then(() => {})
    }
  },
  methods: {
    upload (dataUrl) {},
    postFeed () {
      // 判断是否已经上传
      if (!this.isUploaded()) {

      }
    },
    sharePic () {
      if (!this.isUploaded()) {

      }
    },
    reedit () {
      this.fetchUrl = ''
      this.$emit('reedit')
    },
    isUploaded () {
      // 出错情况
    }
  }
}
</script>

<style lang="scss" scoped>
.finish-bg-header {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 0.98rem;
  background: url("../assets/images/bg_finish_header.png") center/100% no-repeat;
}
.finish-bg-footer {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 0.98rem;
  background: url("../assets/images/bg_finish_footer.png") center/100% no-repeat;
}

.wrap-finish {
  position: relative;
  min-height: calc(100vh - 0.88rem);

  background: url("../assets/images/bg_finish_repeat.png") top left/contain
    repeat-y;

  overflow: auto;
}

.wrap-pre-img {
  position: relative;

  margin: 0.52rem auto 0;
  width: 6.38rem;
  border-radius: 0.2rem;
  font-size: 0;
  background: #fff;
  overflow: hidden;
}

.remote-preview {
  width: 100%;
}

.finish-title {
  margin-left: 0.17rem;
  width: 0.96rem;
  height: 0.42rem;
}

.finish-info {
  margin: 0;

  font-size: 0.28rem;
  font-family: PingFangSC-Medium;
  font-weight: 500;
  color: rgba(118, 118, 118, 1);
}

.btn-publish {
  margin-top: 0.2rem;

  display: inline-block;
  width: 5.28rem;
  height: 0.98rem;

  font-size: 0.36rem;
  font-family: PingFangSC-Medium;
  font-weight: 500;
  color: rgba(0, 0, 0, 1);
  line-height: 0.84rem;
  text-decoration: none;
  background: url("../assets/images/btn_publish.png") center/cover no-repeat;
}

.wrap-sub-btns {
  display: flex;
  justify-content: space-between;

  margin: 0.22rem auto;
  width: 5.28rem;
}

.finish-sub-btn {
  display: inline-flex;
  justify-content: center;
  align-items: center;

  box-sizing: border-box;
  width: 2.42rem;
  height: 0.6rem;
  border-radius: 0.3rem;

  border: 0.06rem solid #ffd324;

  font-size: 0.28rem;
  font-family: PingFangSC-Medium;
  font-weight: 500;
  color: #fff;
  text-decoration: none;
}

.wrap-canvas {
  width: 6.38rem;
  height: 9.46rem;
}

@supports (bottom: constant(safe-area-inset-bottom)) or
  (bottom: env(safe-area-inset-bottom)) {
  .wrap-finish {
    min-height: calc(100% - 0.88rem);
  }
}
</style>
