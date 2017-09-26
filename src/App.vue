<template>
  <div id="app">
    <div id="blur-div">
      <img id="blur-image" src="./assets/1.jpg" alt="">
      <canvas id="canvas"></canvas>
      <a href="javasrcipt:void(0)" class="button reset-button" @click.stop.prevent="reset">RESET</a>
      <a href="javasrcipt:void(0)" class="button show-button" @click.stop.prevent="show">SHOW</a> 
    </div>
  </div>
</template>

<script>

const CANVAS_WIDTH = window.innerWidth
const CANVAS_HEIGHT = window.innerHeight
const ROUND_R = 50
let clippingRegion = {x: -1,y: -1, r: -1}
let leftMargin = 0, topMargin = 0

export default {
  name: 'app',
  mounted() {
      let _this = this
      let image = new Image()
      image.src = require("./assets/1.jpg")
      image.onload = function (e) {

        document.getElementById("blur-div").style.width = CANVAS_WIDTH + 'px'
        document.getElementById("blur-div").style.height = CANVAS_HEIGHT + 'px'

        document.getElementById("blur-image").style.width = image.width + 'px'
        document.getElementById("blur-image").style.height = image.height + 'px'
        leftMargin = (image.width - CANVAS_WIDTH ) / 2
        topMargin = (image.height - CANVAS_HEIGHT ) / 2
        document.getElementById("blur-image").style.top = String(-topMargin) + 'px'
        document.getElementById("blur-image").style.left = String(-leftMargin) + 'px'

        _this.initCanvas(image)
      }
  },
  methods: {
    initCanvas(img) {
      let theLeft = leftMargin < 0 ? -leftMargin:0
      let theTop = topMargin < 0 ? - topMargin:0

      clippingRegion = {
        x: Math.random()*(CANVAS_WIDTH-2*ROUND_R-2*theLeft)+ROUND_R+theLeft,
        y: Math.random()*(CANVAS_HEIGHT-2*ROUND_R-2*theTop)+ROUND_R+theTop,
        r: ROUND_R
      }
      this.draw(img)
    },
    draw(img) {
      let canvas = document.getElementById("canvas")
      canvas.width = CANVAS_WIDTH
      canvas.height = CANVAS_HEIGHT

      let context = canvas.getContext("2d")
      context.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT)
      context.save()
      
      this.setClippingRegion(context)

      context.drawImage(img, 
        leftMargin, topMargin, CANVAS_WIDTH, CANVAS_HEIGHT,
        0, 0, CANVAS_WIDTH, CANVAS_HEIGHT)
      context.restore()
    },
    setClippingRegion(context) {
      context.beginPath()
      context.arc(clippingRegion.x, clippingRegion.y, clippingRegion.r , 0, Math.PI*2, false)
      context.clip()
    },
    show() {
      let _this = this
      let theAnimation =setInterval(
        function() {
          if(clippingRegion.r > Math.max(CANVAS_WIDTH,CANVAS_HEIGHT)) {
            clearInterval(theAnimation)
          }

          clippingRegion.r += 30
          let image = new Image()
          image.src = require("./assets/1.jpg")
          _this.draw(image)
        },
        30
      )
    },
    reset() {
      let image = new Image()
      image.src = require("./assets/1.jpg")
      clippingRegion.r = 50
      this.initCanvas(image)
    }
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
  #app
    #blur-div
      position: relative
      margin: 0 auto
      overflow: hidden
      #blur-image
        display: block
        position: absolute
        left: 0
        top: 0
        margin: 0 auto
        filter: blur(15px)
        z-index: 0
      #canvas
        display: block
        position: absolute
        left: 0
        top: 0
        margin: 0 auto
        z-index: 100
      .button
        display: block
        position: absolute
        z-index: 200
        width: 100px
        height: 30px
        color: white
        text-decoration: none
        text-align: center
        line-height: 30px
        border-radius: 5px
        &.reset-button
          left: 50px
          bottom: 50px
          background-color: #058
        &.reset-button:hover
          background-color: #047
        &.show-button
          right: 50px
          bottom: 50px
          background-color: #085
        &.show-button:hover
          background-color: #074
</style>
