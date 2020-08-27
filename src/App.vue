<template>
  <div class="editor" ref="editor">
    <the-title-bar :title="title" @back="navBack" @close="navClose" />

    <!-- 编辑 -->
    <div v-show="!finishShow">
      <div
        class="canvas"
        @touchstart="
          offset = 0;
          lastImageUrl = '';
        "
      >
        <scene
          :resolution="[canvasWidth * pixelRatio, canvasHeight * pixelRatio]"
          :viewport="[canvasWidth, canvasHeight]"
          ref="screenshots"
        >
          <layer @click="handleLayerClick">
            <sprite
              :bgimage="{
                src: canvasBg,
                display: 'stretch'
              }"
              :size="[canvasWidth * pixelRatio, canvasHeight * pixelRatio]"
            />
            <canvas-group
              v-for="(item, index) of canvasList"
              :key="item.id"
              :image="item"
              :zIndex="item.zIndex"
              :offset="item.offset"
              :isActive="index === activeIndex"
              @select="handleActive(index)"
              @select-bottom="selectBottom(index)"
              @delete="deleteImage(index)"
            />
          </layer>
        </scene>
      </div>

      <div class="wrap-select" :class="{ active: true }">
        <div class="wrap-bar-select" @click="selectShow = !selectShow">
          <span class="bar-info">年味贴纸</span>
          <a
            href="javascript: void(0)"
            class="bar-handler"
            :class="{
              up: !selectShow,
              hidden: disableClickSelect
            }"
          ></a>
          <a
            href="javascript: void(0)"
            class="bar-btn"
            :class="{ disabled: disabledSave }"
            @click.stop="handleSave"
            >生成图片</a
          >
        </div>

        <!-- tab切换 -->
        <select-tabs
          :tabs="tabMap"
          :activeIndex="activeTab"
          @change-tab="index => (this.activeTab = index)"
        />
        <!-- /tab切换 -->

        <select-swapper
          :activeIndex="activeTab"
          :maxIndex="tabMap.length - 1"
          @changePage="handleChangePage"
        >
          <div
            class="wrap-imgs"
            v-for="(list, index) of selectList"
            :key="'list' + index"
            :style="{
              height: selectShow ? '3.9rem' : selectHeight + 'px'
            }"
          >
            <image-select
              v-for="(item, index) of list"
              :key="'item' + index"
              :image="item.url"
              @click="addImage(item)"
            />
          </div>
        </select-swapper>
      </div>

      <the-confirm
        v-if="confirmShow"
        :text="confirmText"
        @confirm="confirmCb"
        @cancel="cancelCb"
      />
    </div>
    <!-- /编辑 -->

    <!-- 完成 -->
    <finish-page
      v-if="finishShow"
      :previewRemote="previewRemote"
      :imgUrl="imgUrl"
      @reedit="reedit"
    />
    <!-- /完成 -->
  </div>
</template>

<script>
import TheTitleBar from './components/TheTitleBar'
import TheConfirm from './components/TheConfirm'
import FinishPage from './components/EditorFinish'
import ImageSelect from './components/ImageSelect'
import SelectTabs from './components/SelectTabs'
import SelectSwapper from './components/SelectSwapper'
import CanvasGroup from './components/CanvasGroup'

const subjectMap = {
  JIZAO: 1,
  NIANHUO: 2,
  NIANYEFAN: 3,
  ZOUQINFANGYOU: 4,
  YINGCAISHEN: 5
}

const titleMap = {
  JIZAO: '祭灶神',
  NIANHUO: '办年货',
  NIANYEFAN: '年夜饭',
  ZOUQINFANGYOU: '拜新年',
  YINGCAISHEN: '迎财神'
}

export default {
  components: {
    TheTitleBar,
    TheConfirm,
    FinishPage,
    ImageSelect,
    SelectTabs,
    SelectSwapper,
    CanvasGroup
  },
  created () {
    const currSubject = 'JIZAO'
    this.title = titleMap[currSubject]

    // // 设置活动Id，加载对应资源
    this.activityId = subjectMap[currSubject] || 1
    this.canvasBg = require(`./assets/images/${this.activityId}/bg.png`)
    const assetsMap = require(`./assets/images/${this.activityId}/index.js`)

    this.selectList = assetsMap.default.map(images => {
      return images.map(image => {
        return {
          url: require(`./assets/images/${this.activityId}/${image.filename}`),
          width: image.width,
          height: image.height
        }
      })
    })
    this.tabMap = assetsMap.maps
  },
  data () {
    return {
      title: '狐友带你寻年味',

      finishShow: false,
      selectShow: false,

      confirmShow: false,
      confirmText: '',

      activityId: 1, // 1:祭灶神 2:办年货  3:年夜饭  4:拜新年  5:迎财神

      canvasWidth: window.innerWidth,
      canvasHeight: window.innerWidth * 1.33333,
      canvasBg: '',
      canvasImgId: 0,
      canvasList: [],
      activeIndex: -1,
      maxZindex: 1000,
      minZindex: 1000,
      pixelRatio: 2,
      // 连续选择同一图片且中间没有操作，偏移20
      lastImageUrl: '',
      offset: 0,

      tabMap: [],
      activeTab: 0,
      selectList: [],

      imgUrl: '',
      generating: false,
      // 预览 远程
      previewRemote: false,

      selectHeight: 0,
      disableClickSelect: false
    }
  },
  computed: {
    disabledSave () {
      return this.canvasList.length < 2 || this.generating
    }
  },
  mounted () {
    // 演示动画
    setTimeout(() => {
      this.selectShow = true
      setTimeout(() => {
        this.selectShow = false
      }, 1000)
    }, 500)

    // 计算选择区高度
    const remToPx = parseFloat(document.documentElement.style.fontSize)
    // iphoneX获取到的this.$refs["editor"].clientHeight高
    setTimeout(() => {
      const bodyHeight = this.$refs.editor.clientHeight
      this.selectHeight =
        bodyHeight -
        /* 标题栏高度 */ (0.88 * remToPx /* 选择区高度 */ +
        this.canvasHeight /* 选择条高度 */ +
          0.84 * remToPx +
        /* tab高度 */ 0.6 * remToPx)
      if (this.selectHeight > (1.8 + 1.8 + 0.3) * remToPx) {
        this.selectHeight = (1.8 + 1.8 + 0.3) * remToPx
        this.disableClickSelect = true
      } else if (this.selectHeight < 0) {
        this.selectHeight = 0
      }
    }, 100)
  },
  methods: {
    handleLayerClick () {
      this.activeIndex = -1
    },
    handleActive (index) {
      this.canvasList[index].zIndex = ++this.maxZindex
      // 没办法阻止.canvas的click的触发。让这里的activeIndex更晚触发
      this.$nextTick(() => {
        this.activeIndex = index
      })
    },
    selectBottom (index) {
      this.canvasList[index].zIndex = --this.minZindex
    },
    handleChangePage (isRight) {
      if (!isRight) {
        this.activeTab++
      } else {
        this.activeTab--
      }
    },
    addImage (image) {
      if (image.url === this.lastImageUrl) {
        this.offset += 20
      } else {
        this.offset = 0
      }
      this.lastImageUrl = image.url
      this.canvasList.push({
        id: this.canvasImgId++,
        url: image.url,
        width: image.width,
        height: image.height,
        zIndex: ++this.maxZindex,
        offset: this.offset
      })
      this.activeIndex = this.canvasList.length - 1
    },
    deleteImage (index) {
      this.canvasList.splice(index, 1)
    },
    saveImage () {
      this.activeIndex = -1
      this.generating = true

      const scene = this.$refs.screenshots

      this.$nextTick(() => {
        // snapshot()异步方法，canvas更新后会执行
        this.$refs.screenshots.snapshot().then(() => {
          this.imgUrl = scene.layers[0].canvas.toDataURL()
          this.finishShow = true
        })
      })
    },
    handleSave () {
      if (this.disabledSave) {
        return
      }
      this.saveImage()
    },
    reedit () {
      this.finishShow = false
      this.previewRemote = false
      this.generating = false
      this.canvasList = []
    },

    showConfirm (text, confirm = () => {}, cancel = () => {}) {
      this.confirmText = text
      this.confirmCb = () => {
        this.confirmShow = false
        confirm()
      }
      this.cancelCb = () => {
        this.confirmShow = false
        cancel()
      }
      this.confirmShow = true
    },
    confirmCb () {},
    cancelCb () {},

    navBack () {
      if (this.finishShow) {
        this.finishShow = false
        this.previewRemote = false
        this.generating = false
      } else if (this.canvasList.length >= 2) {
        this.showConfirm('您尚未生成年味图，是否仍要退出？', () => {
          alert('close')
        })
      } else {
        alert('close')
      }
    },
    navClose () {
      if (this.finishShow) {
        alert('close')
      } else if (this.canvasList.length >= 2) {
        this.showConfirm('您尚未生成年味图，是否仍要退出？', () => {
          alert('close')
        })
      } else {
        alert('close')
      }
    }
  }
}
</script>

<style lang="scss">
html,
body {
  margin: 0;
  padding: 0;
  overflow: hidden;
}

* {
  user-select: none;
}
</style>

<style lang="scss" scoped>
// 编辑区

.canvas {
  position: relative;
  height: 10rem;

  background: #fff center/cover no-repeat;
  overflow: hidden;
}

// 选择区

.wrap-select {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  background: #fff;

  transform: translate3d(0, 4.5rem, 0);

  transition: all 0.5s ease 0s;
}

.wrap-select.active {
  transform: translate3d(0, 0, 0);
}

.wrap-bar-select {
  display: flex;
  justify-content: space-between;
  align-items: center;

  padding: 0 0.28rem;
  font-size: 0.24rem;
  font-family: PingFangSC-Regular;
  font-weight: 400;

  height: 0.84rem;
}

.bar-handler {
  flex-grow: 1;

  height: 100%;
  font-size: 0;
  background: url("./assets/images/btn_collapse.png") center/0.55rem 0.55rem
    no-repeat;

  transition: all 0.2s ease 0.3s;
}

.bar-handler.up {
  transform: rotate(180deg);
}

.bar-handler.hidden {
  visibility: hidden;
}

.bar-btn {
  padding: 0.09rem 0;
  color: #000;
  text-decoration: none;
  background: #ffd324;
  border: 1px solid #000;
  border-radius: 0.22rem;
  text-align: center;
}

.bar-btn.disabled {
  background: rgba(211, 211, 211, 1);
  border: 1px solid rgba(211, 211, 211, 1);
  color: #767676;
}

.bar-info {
  color: #767676;
}

.bar-info,
.bar-btn {
  display: inline-block;
  box-sizing: border-box;
  width: 1.6rem;
}

.wrap-imgs {
  display: inline-block;
  white-space: normal;

  box-sizing: border-box;
  font-size: 0;
  margin-left: -0.3rem;
  padding: 0 0.3rem 0.3rem 0.3rem;
  height: 3.88rem;
  vertical-align: top;

  background: #f9f9f9;
  overflow-y: auto;
  transition: all 0.5s ease 0s;
}

.editor {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
  overflow: hidden;
}

@supports (bottom: constant(safe-area-inset-bottom)) or
  (bottom: env(safe-area-inset-bottom)) {
  .editor {
    position: absolute;
    top: 0;
    top: constant(safe-area-inset-top); /* iOS 11.0-iOS 11.1 */
    top: env(safe-area-inset-top);
    bottom: 0;
    bottom: constant(safe-area-inset-bottom); /* iOS 11.0-iOS 11.1 */
    bottom: env(safe-area-inset-bottom);
    width: 100%;
    overflow: hidden;
  }

  .wrap-select {
    position: absolute;
    bottom: 0;
  }
}
</style>
