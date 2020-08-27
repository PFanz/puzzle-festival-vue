<template>
  <div class="title-bar">
    <a
      href="javascript: void(0)"
      class="title-bar-btn-back"
      @click="$emit('back')"
    ></a>
    <span class="title-bar-text">{{ title }}</span>
    <a
      href="javascript: void(0)"
      class="title-bar-btn-close"
      @click="$emit('close')"
    ></a>
  </div>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      default: '&nbps;'
    }
  }
}
</script>

<style scoped>
.title-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 0.88rem;
}

.title-bar-btn-back {
  width: 0.88rem;
  height: 0.88rem;
  background: url("../assets/images/ic_back_bar.png") center/6.1vw 6.1vw
    no-repeat;
}

.title-bar-btn-close {
  width: 0.88rem;
  height: 0.88rem;
  background: url("../assets/images/ic_close_bar.png") center/6.1vw 6.1vw
    no-repeat;
}

.title-bar-text {
  font-size: 0.36rem;
  font-family: PingFangSC-Medium;
  font-weight: 500;
  color: rgba(69, 69, 69, 1);

  max-width: 6rem;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>
