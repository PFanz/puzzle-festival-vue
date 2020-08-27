<template>
  <div
    class="wrap-swapper"
    :style="{
      transform: `translate3d(${translateX}, 0, 0)`
    }"
    @touchstart="handleMoveStart"
    @touchmove="handleMoving"
    @touchend="handleMoveEnd"
  >
    <slot></slot>
  </div>
</template>

<script>
export default {
  props: {
    activeIndex: {
      type: Number,
      default: 0
    },
    maxIndex: {
      type: Number
    }
  },
  data () {
    return {
      startX: 0,
      startY: 0,
      isMoveHorizontal: undefined,
      translateX: -7.5 * this.activeIndex + 'rem'
    }
  },
  watch: {
    activeIndex: function () {
      this.translateX = -7.5 * this.activeIndex + 'rem'
    }
  },
  methods: {
    handleMoveStart (event) {
      this.startX = event.touches[0].clientX
      this.startY = event.touches[0].clientY
    },
    handleMoving (event) {
      const { clientX, clientY } = event.touches[0]
      if (clientX < this.startX && this.activeIndex === this.maxIndex) {
        this.startX = clientX
        this.startY = clientY
        return
      }
      if (clientX > this.startX && this.activeIndex === 0) {
        this.startX = clientX
        this.startY = clientY
        return
      }
      // 判断方向
      if (this.isMoveHorizontal === undefined) {
        if (Math.abs(clientX - this.startX) > Math.abs(clientY - this.startY)) {
          this.isMoveHorizontal = true
        } else {
          this.isMoveHorizontal = false
        }
      } else if (this.isMoveHorizontal === true) {
        event.preventDefault()
        this.translateX =
          clientX - this.startX - this.activeIndex * window.innerWidth + 'px'
      }
    },
    handleMoveEnd (event) {
      const { clientX } = event.changedTouches[0]
      if (Math.abs(clientX - this.startX) > window.innerWidth / 5) {
        this.$emit('changePage', clientX - this.startX > 0)
      } else {
        // 返回原来位置
        this.translateX = -7.5 * this.activeIndex + 'rem'
      }
      this.isMoveHorizontal = undefined
    }
  }
}
</script>

<style lang="scss" scoped>
.wrap-swapper {
  white-space: nowrap;
  font-size: 0;

  transition: 0.1s all linear;
}
</style>
