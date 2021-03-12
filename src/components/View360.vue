<template>
  <div class="view-360" :class="ready && 'ready'" id="wrapper">
    <canvas id="360-canvas" class="image" />
    <img class="poster" src="../assets/models/model1.png" />
  </div>
</template>

<script>
import PhyTouch from 'phy-touch'

export default {
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
      return Promise.all(this.getModelImages().map(url => {
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
    getModelImages() {
      return new Array(31).fill('').map((item, index) => {
        return require(`../assets/models/model${index + 1}.png`)
      })
    },
    init() {
      this.loadAllResource().then((resources) => {
        this.models = resources
        this.initWrapper()
      }).catch((err) => {
        console.log(err)
      });
    },
    initWrapper() {
      var phyTouch = new PhyTouch({
        touch:"#wrapper",//反馈触摸的dom
        vertical: false,//不必需，默认是true代表监听竖直方向touch
        target: { x: 0 }, //运动的对象
        property: "x",  //被运动的属性
        sensitivity: 0.1,//不必需,触摸区域的灵敏度，默认值为1，可以为负数
        step: 1,//用于校正到step的整数倍
        bindSelf: false,
        maxSpeed: 1, //不必需，触摸反馈的最大速度限制 
        value: 0,
        change:(value) => { 
          console.log(value)
          this.drawImage(value)
        },
        touchEnd() {
          phyTouch.stop()
        }
      })
      this.canvas = document.getElementById('360-canvas')
      this.canvas.width = window.innerWidth * this.devicePixelRatio
      this.canvas.height = 210 * this.devicePixelRatio
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
  width: 100%;
  height: 211px;
  margin-top: 25vh;
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