<template>
  <div 
    id="wrapper"
    class="view-360" 
    :class="ready && 'ready'"  
    :style="`width: ${width}px;height: ${height}px`">
    <canvas id="360-canvas" class="image" />
    <img class="poster" :src="poster" />
  </div>
</template>

<script>
import PhyTouch from 'phy-touch'

export default {
  props: {
    modelImages: {
      type: Array,
      default: () => {
        return []
      }
    },
    poster: {
      type: String,
      default: ''
    },
    width: {
      type: Number,
      default: 375
    },
    height: {
      type: Number,
      default: 210
    }
  },
  mounted() {
    this.init()
  },
  data() {
    return {
      current: 0,
      currentX: 0,
      lastX: 0,
      models: [],
      devicePixelRatio: Math.round(window.devicePixelRatio || 1),
      ready: false
    }
  },
  methods: {
    loadAllResource() {
      return Promise.all(this.modelImages.map(url => {
        return this.loadImage(url)
      }))
    },
    loadImage(url) {
      return new Promise((resolve, reject) => {
        const image = new Image()
        image.src = url
        image.onload = function() {
          resolve(image)
        }
        image.onerror = function(err) {
          reject(err)
        }
      })
    },
    init() {
      this.loadAllResource().then((resources) => {
        this.models = resources
        this.initWrapper()
        this.initCanvas()
      }).catch((err) => {
        console.log(err)
      });
    },
    initWrapper() {
      var phyTouch = new PhyTouch({
        touch:"#wrapper",
        vertical: false,
        sensitivity: 0.1,
        step: 1,
        maxSpeed: 1,
        value: 0,
        change:(value) => { 
          this.drawImage(value)
        },
        touchEnd() {
          phyTouch.stop()
        }
      })
    },
    initCanvas() {
      this.canvas = document.getElementById('360-canvas')
      this.canvas.width = this.width * this.devicePixelRatio
      this.canvas.height = this.height * this.devicePixelRatio
			this.ctx = this.canvas.getContext('2d')
      this.drawImage(0)
    },
    drawImage(value) {
      let index = Math.abs(parseInt(value % 31 )) 
      if (value > 0) index = 30 - index
      const image = this.models[30 - index]
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height) 
      this.ctx.drawImage(image, 0, 0, this.canvas.width, this.canvas.height)
      if (!this.ready) this.ready = true
    }
  }
}
</script>

<style scoped>
#wrapper{
  position: relative;
  pointer-events: none;
}

#wrapper.ready{
  pointer-events: all;
}

img, canvas{
  width: 100%;
  height: 211px;
  position: absolute;
  left: 0px;
  top: 0px;
  display: block;
  pointer-events: none;
}

.poster{
  opacity: 1;
}
.ready .poster{
  opacity: 0;
}

</style>