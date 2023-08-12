<template>
  <header>
    <h1>Welcome to 4kBuddy</h1>
    <p>
      Just <b>drag the slider</b> and you will see estimated performance gain and how much screen
      space custom resolution will take
    </p>
    <p>
      Your resolution is <b>{{ screenResolution }}</b>
      <br />
      with
      <b>{{ this.ratioWidth + ' : ' + this.ratioHeight }}</b> aspect ratio
      <br />
      <sub>(if not correct, make sure page is not zummed in/out, try to press <b>CTRL + 0</b>)</sub>
    </p>
    <FullscreenToggle />
    <form action="" @submit.prevent="">
      <input type="range" min="0" :max="downscaleRes.length - 1" v-model="selectedRes" />
      <div class="estimated-performance" v-if="downscaleRes.length > 0">
        <label>
          Initial fps
          <input class="estimated-performance__initial" type="number" min="10" v-model="initial" />
        </label>
        <label>
          Estimated fps
          <input class="estimated-performance__estimate" type="number" :value="estimate" readonly />
        </label>
        <span class="estimated-performance__percentage"
          >Percentage gain <b>{{ downscaleRes[selectedRes].lessBy }}%</b></span
        >
      </div>
    </form>
    <iframe
      style="width: 100%; aspect-ratio: 16/9"
      src="https://www.youtube.com/embed/V5ii3ld2J4c"
      title="YouTube video player"
      frameborder="0"
      allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
      allowfullscreen
    ></iframe>
    <div class="links">
      <a class="icon" href="https://github.com/DanilShapilov/4kBuddy" target="_blank"
        ><img src="/github-mark-white.svg" alt="" srcset=""
      /></a>
    </div>
  </header>

  <main>
    <ScreenWindow
      v-if="downscaleRes.length > 0"
      :screen-res="downscaleRes[selectedRes]"
      :screen-color="colors[selectedRes % colors.length]"
    />
  </main>
</template>

<script>
import FullscreenToggle from './components/FullscreenToggle.vue'
import ScreenWindow from './components/ScreenWindow.vue'
import { round } from 'mathjs'

const AMOUNT_OF_RESULTS = 80

export default {
  components: {
    FullscreenToggle,
    ScreenWindow
  },
  data() {
    return {
      width: this.getWidth(),
      height: this.getHeight(),
      pixels: this.getWidth() * this.getHeight(),
      ratio1: this.getWidth() / this.getHeight(),
      ratio2: this.getHeight() / this.getWidth(),
      ratio: 'Ratio: ',
      colors: this.getColors(),
      ratioWidth: 1,
      ratioHeight: 1,
      verifiedWidth: 1,
      verifiedHeight: 1,
      checkWidth: 1,
      downscaleRes: [],
      selectedRes: 0,
      initial: 35
    }
  },

  methods: {
    reset() {
      this.width = this.getWidth()
      this.height = this.getHeight()
      this.pixels = this.getWidth() * this.getHeight()
      this.ratio1 = this.getWidth() / this.getHeight()
      this.ratio2 = this.getHeight() / this.getWidth()
      this.ratio = 'Ratio: '
      this.ratioWidth = 1
      this.ratioHeight = 1
      this.verifiedWidth = 1
      this.verifiedHeight = 1
      this.checkWidth = 1
      this.downscaleRes = []
      this.selectedRes = 0
    },
    getWidth() {
      return window.screen.width * window.devicePixelRatio
    },
    getHeight() {
      return window.screen.height * window.devicePixelRatio
    },
    getColors() {
      return Array.apply(null, Array(AMOUNT_OF_RESULTS)).map(
        () =>
          `hsl(${360 * Math.random()}, ${25 + 70 * Math.random()}%, ${85 + 10 * Math.random()}%)`
      )
    },
    onScreenChange() {
      this.reset()
      this.$nextTick(() => {
        this.doShit()
      })
    },
    doShit() {
      // Aspect Ratio Start
      for (let i = 1; i <= this.height; i++) {
        this.checkWidth = round(this.ratio1 * i, 2)

        if (this.checkWidth % 1 < 0.1) {
          this.ratioWidth = round(this.checkWidth, 0)
          this.ratioHeight = i

          if (this.ratioWidth === 8 && this.ratioHeight === 5) {
            this.ratioWidth = 16
            this.ratioHeight = 10
          }
          break
        }
      }
      // Aspect Ratio End
      const newDownscaleRes = []
      for (
        let loopHeight = this.height - this.ratioHeight;
        loopHeight > this.height - this.ratioHeight - this.ratioHeight * AMOUNT_OF_RESULTS;
        loopHeight = loopHeight - this.ratioHeight
      ) {
        if (this.verifiedWidth > 0 && this.verifiedHeight > 0) {
          this.verifiedWidth = round(this.ratio1 * loopHeight, 0)
          this.verifiedHeight = round(this.verifiedWidth / this.ratio1, 0)

          const pixels = this.verifiedWidth * this.verifiedHeight
          newDownscaleRes.push({
            width: this.verifiedWidth,
            height: this.verifiedHeight,

            lessBy: round(((this.pixels - pixels) / this.pixels) * 100, 2)
          })
        }
      }
      this.downscaleRes = newDownscaleRes
    }
  },
  computed: {
    screenResolution() {
      return `${this.width} x ${this.height}`
    },
    estimate() {
      return round(this.initial * (this.downscaleRes[this.selectedRes].lessBy / 100 + 1), 2)
    }
  },

  watch: {},
  mounted() {
    // this.onScreenChange = debounce(this.onScreenChange, 150)
    window.screen.addEventListener('change', this.onScreenChange)
    // window.addEventListener('resize', this.onScreenChange)
    this.onScreenChange()
  },

  beforeDestroy() {
    window.screen.removeEventListener('change', this.onScreenChange)
    // window.removeEventListener('resize', this.onScreenChange)
  }
}
</script>
<style scoped>
header {
  position: fixed;
  left: 20rem;
  top: 50%;
  transform: translateY(-50%);
  z-index: 100;
  max-width: 40rem;
  text-align: left;
  display: flex;
  flex-direction: column;
  align-items: start;
  gap: 2rem;
  background-color: rgba(0, 0, 0, 0.3);
  padding: 2rem;
  border-radius: 0.6rem;
}
input[type='range'] {
  width: 30rem;
  cursor: pointer;
}

.icon {
  width: 5rem;
  height: 5rem;
  display: flex;
}

.estimated-performance {
  display: flex;
  flex-direction: column;
  text-align: left;
  align-items: start;
  gap: 0.5rem;
}
.estimated-performance input {
  font-size: 1.8rem;
  background-color: #111;
  border: 1px solid #999;
  border-radius: 0.6rem;
  color: #fff;
  width: 7rem;
  padding: 0.2rem;
}
.estimated-performance__percentage b {
  color: #1ad13b;
}
</style>
