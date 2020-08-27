<template>
  <div class="confirm-cover">
    <div class="wrap-confirm">
      <div class="confirm-body">
        <div class="confirm-body-text">{{ text }}</div>
      </div>
      <div class="wrap-btns">
        <a
          href="javascript: void(0)"
          class="confirm-btn"
          @click="$emit('cancel')"
          >否</a
        >
        <a
          href="javascript: void(0)"
          class="confirm-btn"
          @click="$emit('confirm')"
          style="color: #FE5B62;"
          >是</a
        >
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    text: {
      type: String,
      default: ''
    }
  }
}
</script>

<style lang="css" scoped>
.confirm-cover {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;

  display: flex;
  justify-content: center;
  align-items: center;

  background: rgba(0, 0, 0, .4);
}

.wrap-confirm {
  width: 68.53vw;
  height: 59.2vw;
  background: transparent url("../assets/images/bg_dialogue.png") center/cover no-repeat;
}

.confirm-body {
  margin: 20vw auto 0;

  display: flex;
  justify-content: center;
  align-items: center;

  width: 49vw;
  height: 12vw;

  font-size: 3.7vw;
  font-family: PingFangSC-Regular;
  font-weight: 400;
  line-height: 5.3vw;
}

.confirm-body-text {
  max-height: 10.6vw;
  overflow: hidden;
}

.wrap-btns {
  margin-top: 10vw;
}

.confirm-btn {
  margin-left: 6vw;
  display: inline-block;
  width: 24vw;
  line-height: 8vw;
  vertical-align: top;

  font-size: 4.3vw;
  font-family: PingFangSC-Regular;
  color: #000;
  font-weight: 400;
  text-decoration: none;

  text-align: center;
}
</style>
