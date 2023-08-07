<template>
  <button type="button" @click="toggleFullScreen"><span>Toggle Fullscreen</span></button>
</template>

<script>
export default {
  methods: {
    toggleFullScreen() {
      if (!document.fullscreenElement) {
        document.documentElement.requestFullscreen()
      } else if (document.exitFullscreen) {
        document.exitFullscreen()
      }
    }
  }
}
</script>
<style scoped>
button {
  align-items: center;
  background-image: linear-gradient(144deg, #af40ff, #5b42f3 50%, #00ddeb);
  border: 0;
  border-radius: 0.8rem;
  box-shadow: rgba(151, 65, 252, 0.2) 0 1.5rem 3rem -0.5rem;
  box-sizing: border-box;
  color: #ffffff;
  display: flex;
  font-size: 2rem;
  justify-content: center;
  line-height: 1em;
  max-width: 100%;
  min-width: 14rem;
  padding: 0.3rem;
  text-decoration: none;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  white-space: nowrap;
  cursor: pointer;
}
button:active,
button:hover {
  outline: 0;
}
button span {
  background-color: rgb(5, 6, 45);
  padding: 1.6rem 2.4rem;
  border-radius: 0.6rem;
  width: 100%;
  height: 100%;
  transition: 300ms;
}
button:hover span {
  background: none;
}
</style>
