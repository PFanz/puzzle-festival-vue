<template>
  <span
    class="image-select"
    :class="{ 'skeleton-45': !loaded }"
    :style="{ backgroundImage: `url(${image})` }"
    @click="$emit('click')"
  ></span>
</template>

<script>
export default {
  props: {
    image: {
      type: String,
      default: ''
    }
  },
  data () {
    return {
      loaded: false
    }
  },
  created () {
    const img = new Image()
    img.src = this.image
    img.onload = () => {
      this.loaded = true
    }
  }
}
</script>

<style lang="scss">
.image-select {
  display: inline-block;
  margin: 0.3rem 0 0 0.3rem;
  width: 1.5rem;
  height: 1.5rem;
  background: transparent center/contain no-repeat;
}
</style>
