<template>
  <div class="hut-progressbar">
    <canvas v-if="canvasId" :style="canvasUnit" class="hut-progress-inner" :canvas-id="canvasId"></canvas>
    <canvas v-if="canvasId" :style="canvasUnit" class="hut-progress" :canvas-id="canvasId + '-inner'"></canvas>
    <div class="hut-text" v-if="text">
      <span v-html="text"></span>
    </div>
  </div>
</template>

<script>
  const CANVAS_UNIT = 150

  export default {
    data () {
      return {
      }
    },
    props: {
      // canvasId
      canvasId: {
        type: String,
        required: true
      },
      // 进度百分比
      percent: {
        type: Number,
        default: 0.5
      },
      // canvas 宽度和高度
      unit: {
        type: String,
        default: '150'
      },
      // 进度条背景颜色
      strokeBgColor: {
        type: String,
        default: '#f3f3f3'
      },
      // 进度条颜色
      strokeColor: {
        type: String,
        default: '#EA3B76'
      },
      // 进度条宽度
      strokeWidth: {
        type: Number,
        default: 4
      },
      // 圆形或者圆形缺口
      strokeType: {
        type: String,
        default: 'gap'
      },
      // 圆形或者圆形缺口
      text: {
        type: String,
        default: ''
      }
    },
    computed: {
      canvasUnit () {
        return `width:${this.unit}rpx;height:${this.unit}rpx`
      }
    },
    watch: {
      percent (newValue) {
        this.getRpx().then(rpx => {
          let canvasBg = this.canvasId + '-inner'
          this.drawProgressbg(rpx, this.canvasId)
          this.drawProgress(rpx, newValue, canvasBg)
        })
      }
    },
    methods: {
      // 获取自适应单位
      getRpx () {
        let rpx = 1 // 相对单位
        return new Promise(resolve => {
          wx.getSystemInfo({
            success: function (res) {
              rpx = res.windowWidth / 375
              resolve(rpx)
            }
          })
        })
      },
      // 进度条背景
      drawProgressbg (rpx, canvasId) {
        let ctxStart, ctxEnd
        if (this.strokeType === 'gap') {
          ctxStart = 0.3
          ctxEnd = 0.7
        } else {
          ctxStart = 0
          ctxEnd = 2
        }
        let ctx = wx.createCanvasContext(canvasId)
        ctx.setLineWidth(this.strokeWidth) // 设置圆环的宽度
        ctx.setStrokeStyle(this.strokeBgColor) // 设置圆环的颜色
        ctx.setLineCap('round') // 设置圆环端点的形状
        ctx.beginPath()
        ctx.arc(
                37 * rpx * parseInt(this.unit) / CANVAS_UNIT,
                37 * rpx * parseInt(this.unit) / CANVAS_UNIT,
                25 * rpx * parseInt(this.unit) / CANVAS_UNIT,
                ctxStart * Math.PI,
                ctxEnd * Math.PI,
                true
        )
        ctx.stroke()
        ctx.draw()
      },
      // 进度条
      drawProgress (rpx, value, canvasId) {
        let ctxStart, ctxEnd, ctxRand
        if (this.strokeType === 'gap') {
          ctxStart = 0.7
          ctxEnd = 2.3
        } else {
          ctxStart = 0.5
          ctxEnd = 2.5
        }
        ctxRand = ctxEnd - ctxStart
        let ctx = wx.createCanvasContext(canvasId)
        ctx.draw()
        if(value === 0) {
          ctx.setLineWidth(0) // 设置圆环的宽度
        } else {
          ctx.setLineWidth(this.strokeWidth) // 设置圆环的宽度
        }
        ctx.setStrokeStyle(this.strokeColor) // 设置圆环的颜色
        ctx.beginPath() // 开始一个新的路径
        ctx.arc(
                37 * rpx * parseInt(this.unit) / CANVAS_UNIT,
                37 * rpx * parseInt(this.unit) / CANVAS_UNIT,
                25 * rpx * parseInt(this.unit) / CANVAS_UNIT,
                ctxStart * Math.PI,
                (ctxStart + ctxRand * value) * Math.PI,
                false
        )
        ctx.stroke()
        ctx.draw()
      }
    },
    components: {},
    mounted () {
      this.getRpx().then(rpx => {
        let canvasBg = this.canvasId + '-inner'
        this.drawProgressbg(rpx, this.canvasId)
        this.drawProgress(rpx, this.percent, canvasBg)
      })
    },
    onLoad () {
      this.getRpx().then(rpx => {
        let canvasBg = this.canvasId + '-inner'
        this.drawProgressbg(rpx, this.canvasId)
        this.drawProgress(rpx, this.percent, canvasBg)
      })
    }
  }
</script>

<style scoped>
  .hut-progressbar {
    position: relative;
    width: 100%;
    height: 100%;
    text-align: center;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .hut-progressbar .hut-progress-inner{
    position: absolute;
  }

  .hut-progressbar .hut-text {
    position: absolute;
    align-items: center;
    justify-content: center;
  }
</style>
