<template>
  <div
    class="screen-window"
    :style="{
      border: `0.3rem solid ${screenColor}`,
      width: `${screenRes.width}px`,
      height: `${screenRes.height}px`
    }"
  >
    <p class="screen-window__title">{{ `${screenRes.width}x${screenRes.height}` }}</p>
    <div
      class="screen-window__content"
      :style="{
        backgroundColor: screenColor,
        opacity: 0.15
      }"
    ></div>
  </div>
</template>

<script>
export default {
  props: {
    screenColor: String,
    screenRes: Object
  }
}
</script>

<style scoped>
.screen-window {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  box-sizing: border-box;
  transition:
    all 0.15s,
    border-color 0.5s;
}
.screen-window__title {
  position: absolute;
  margin: 0.5rem 0 0 0.5rem;
  z-index: 1;
  font-size: 3rem;
}
.screen-window__content {
  width: 100%;
  height: 100%;
  transition:
    all 0.15s,
    background-color 0.5s;
}
</style>
