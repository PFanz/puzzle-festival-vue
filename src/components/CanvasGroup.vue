<template>
  <group
    :x="left"
    :y="top"
    :rotate="rotate"
    :anchor="[0.5, 0.5]"
    :size="[width + iconWidth + 4, height + iconWidth + 4]"
    :zIndex="zIndex"
    @click.stop="handleClick"
    @touchstart="handleMoveStart"
    @touchmove="handleMoving"
    @touchend="handleTransformEnd"
  >
    <sprite
      :textures="image.url"
      :size="[width, height]"
      :border="isActive ? [4, 'white'] : [4, 'transparent']"
      :pos="[iconWidth / 2 - 2, iconWidth / 2 - 2]"
      ref="canvasItem"
    />
    <sprite
      :textures="DeleteIcon"
      :size="[iconWidth, iconWidth]"
      :style="isActive ? {} : { display: 'none' }"
      @click.stop="$emit('delete')"
    />
    <sprite
      :textures="BottomIcon"
      :pos="[width + 4, 0]"
      :size="[iconWidth, iconWidth]"
      :style="isActive ? {} : { display: 'none' }"
      @click.stop="$emit('select-bottom')"
    />
    <sprite
      :textures="HandleIcon"
      :pos="[width + 4, height + 4]"
      :size="[iconWidth, iconWidth]"
      :style="isActive ? {} : { display: 'none' }"
      ref="handleItem"
    />
  </group>
</template>

<script>
import DeleteIcon from '../assets/images/ic_close_color.png'
import HandleIcon from '../assets/images/btn_rotation.png'
import BottomIcon from '../assets/images/ic_zhidi.png'

export default {
  props: {
    image: {
      type: Object,
      required: true
    },
    isActive: {
      type: Boolean,
      required: true
    },
    zIndex: {
      type: Number,
      default: 0
    },
    offset: {
      type: Number,
      default: 0
    }
  },
  data () {
    return {
      DeleteIcon,
      HandleIcon,
      BottomIcon,
      iconWidth: 66,

      width: this.image.width / 2.5,
      // width: this.image.width / 2,
      height: this.image.height / 2.5,
      // height: this.image.height / 2,
      left: 750 / 2 + this.offset,
      // left: 0,
      top: 400 + this.offset,
      // top: 0,
      rotate: 0,

      startX: 0,
      startY: 0,
      startWidth: 0,
      startHeight: 0,

      maxWidth: this.image.width * 1.4,
      maxHeight: this.image.height * 1.4,
      minWidth: this.image.width / 6,
      minHeight: this.image.height / 6,

      isTransform: false
    }
  },
  methods: {
    handleMoveStart (event) {
      event.stopDispatch()
      if (event.target === this.$refs.handleItem) {
        this.handleTransformStart(event)
        this.isTransform = true
        return
      }
      this.startX = event.x - this.left
      this.startY = event.y - this.top
    },
    handleMoving (event) {
      event.stopDispatch()
      if (this.isTransform) {
        this.handleTransforming(event)
        return
      }
      this.left = event.x - this.startX
      this.top = event.y - this.startY
    },
    handleTransformStart (event) {
      event.stopDispatch()
      this.startWidth = this.width
      this.startHeight = this.height
    },
    handleTransforming (event) {
      event.stopDispatch()
      // 到中心点坐标
      const [x, y] = this.$refs.canvasItem
        .pointToOffset(event.x, event.y)
        .map(Math.round)
      // 到中心点距离
      // console.log(
      //   x + this.iconWidth / 2 - this.left, // 0
      //   y + this.iconWidth / 2 - this.top // 0
      // );
      const offsetX = x + this.iconWidth / 2 - this.left
      const offsetY = y + this.iconWidth / 2 - this.top
      // 旋转
      if (offsetY > 0 && offsetX > 0) {
        this.rotate =
          ((Math.atan(offsetY / offsetX) -
            Math.atan(this.startHeight / this.startWidth)) /
            Math.PI) *
          180
      } else if (offsetY < 0 && offsetX < 0) {
        this.rotate =
          ((Math.atan(offsetY / offsetX) -
            Math.atan(this.startHeight / this.startWidth)) /
            Math.PI) *
            180 +
          180 -
          Math.atan(this.startHeight / this.startWidth)
      } else if (offsetY > 0 && offsetX < 0) {
        this.rotate =
          ((Math.atan(offsetY / offsetX) -
            Math.atan(this.startHeight / this.startWidth)) /
            Math.PI) *
            180 +
          180
      } else {
        this.rotate =
          ((Math.atan(offsetY / offsetX) -
            Math.atan(this.startHeight / this.startWidth)) /
            Math.PI) *
          180
      }
      // 放大
      const scale =
        Math.pow(Math.pow(offsetX, 2) + Math.pow(offsetY, 2), 0.5) /
        Math.pow(
          Math.pow(this.startWidth / 2, 2) + Math.pow(this.startHeight / 2, 2),
          0.5
        )

      if (this.startWidth * scale > this.maxWidth) {
        this.width = this.maxWidth
        this.height = this.maxHeight
      } else if (this.startWidth * scale < this.minWidth) {
        this.width = this.minWidth
        this.height = this.minHeight
      } else {
        this.width = this.startWidth * scale
        this.height = this.startHeight * scale
      }
    },
    handleTransformEnd () {
      this.isTransform = false
      // 摆正，优化处理
      if (Math.abs(this.rotate) < 4) {
        this.rotate = 0
      }
    },
    handleClick (event) {
      event.stopDispatch()
      this.$emit('select')
    }
  }
}
</script>
