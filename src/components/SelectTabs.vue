<template>
  <div>
    <div class="select-tabs">
      <a
        v-for="(tab, index) of tabs"
        :key="index"
        href="javascript: void(0)"
        class="btn-select-tab"
        :class="{
          active: index === activeIndex
        }"
        :style="{
          width: tabWidth
        }"
        @click="$emit('change-tab', index)"
      >
        <span class="wrap-tab-text"> {{ tab }} </span>
      </a>
    </div>
    <div class="wrap-active-bar">
      <div class="active-bar" :style="{ left: offsetLeft, width: tabWidth }">
        <span class="active-bar-item" :style="{ width: barWidth }"></span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    tabs: {
      type: Array,
      required: true
    },
    activeIndex: {
      type: Number,
      required: true
    }
  },
  computed: {
    tabWidth () {
      return 100 / this.tabs.length + '%'
    },
    barWidth () {
      return this.tabs[this.activeIndex].length + 'em'
    },
    offsetLeft () {
      return this.activeIndex * (100 / this.tabs.length) + '%'
    }
  }
}
</script>

<style lang="scss" scoped>
.select-tabs {
  font-size: 0;
}

.btn-select-tab {
  font-size: 0.24rem;
  display: inline-block;
  text-align: center;
}

.btn-select-tab.active {
  .wrap-tab-text {
    color: #000;
  }
}

.wrap-tab-text {
  position: relative;
  display: inline-block;
  height: 0.6rem;
  line-height: 0.6rem;
  font-family: PingFangSC-Medium;
  font-weight: 500;
  color: #767676;
}

.wrap-active-bar {
  margin-top: -2px;
  height: 2px;
  position: relative;
  z-index: 1;
}

.active-bar {
  position: absolute;
  top: 0;
  left: 0;
  font-size: 0;
  text-align: center;
  height: 2px;

  transition: all 0.1s linear;
}

.active-bar-item {
  font-size: 0.24rem;
  display: inline-block;
  height: 100%;
  width: 2em;
  border-radius: 4px;
  background: #000;
  vertical-align: top;
  transition: all 0.1s linear;
}
</style>
