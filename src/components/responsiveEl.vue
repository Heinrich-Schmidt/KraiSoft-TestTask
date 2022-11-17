<template>
  <div class="fullscreen" 
  @mousemove="mouseMove"
  @mouseup="mouseUp"
  @mousedown="mouseDown"
  @touchstart="startDrag"
  @touchmove="moveDrag"
  @touchend="endDrag"
  ></div>
  <div id="responsiveEl">
    <InteractiveEl :position="currentXY" :size="interactiveElSize" />
    <div class="borders">
      <div id="top" v-bind:class="{show: borders.top.show, hide: borders.top.hide}"></div>
      <div id="bottom" v-bind:class="{show: borders.bottom.show, hide: borders.bottom.hide}"></div>
      <div id="left" v-bind:class="{show: borders.left.show, hide: borders.left.hide}"></div>
      <div id="right" v-bind:class="{show: borders.right.show, hide: borders.right.hide}"></div>
    </div>
  </div>
</template>

<script>
import InteractiveEl from '../components/interactiveEl.vue'

export default {
  name: 'ResponsiveEl',
  components: {
    InteractiveEl
  },
  data() {
    return {
      currentXY: [0, 0], //текущие координаты интерактивного элемента
      lastXY: [0, 0], //координаты интерактивного элемента после последнего клика
      maximalXY:[0, 0], //максимальные значения координат
      clickXY:[0, 0], //координаты клика/тача
      mouseDownFlag: false, //флаг активного клика/тача
      interactiveElSize: [160, 160], //размер интерактивного элемента
      responsiveElSize: [0, 0], //размер отзывчивой области
      windowSize: [0, 0], //размер окна
      marginXY: [0, 0], //отступ от края вьюпорта до отзывчивой области
      borders: { //объект для анимаций бордеров
        top: {
          show: false,
          hide: false
        },
        bottom: {
          show: false,
          hide: false
        },
        left: {
          show: false,
          hide: false
        },
        right: {
          show: false,
          hide: false
        },
      }
    }
  },
  mounted() {
    this.setSizes()
  },
  created() {
    window.addEventListener("resize", this.resize);
  },
  unmounted() {
    window.removeEventListener("resize", this.resize);
  },
  methods: {
    resize() {
      if(this.windowSize[0]!=0)
      this.setSizes()
    },
    mouseDown(event) {
      this.start(event)
    },
    mouseUp() {
      this.stop()
    },
    mouseMove(event) {
      this.move(event)
    },
    startDrag(event){
      this.start(event.touches[0])
    },
    endDrag(){
      this.stop()
    },
    moveDrag(event){
      this.move(event.touches[0])
    },
    start(event){
      if( //проверка на клик по области интерактивного объекта
      event.clientX - this.marginXY[0] >= this.currentXY[0] && 
      event.clientX - this.marginXY[0] <= this.currentXY[0]+this.interactiveElSize[0] &&
      event.clientY - this.marginXY[1] >= this.currentXY[1] &&
      event.clientY - this.marginXY[1] <= this.currentXY[1]+this.interactiveElSize[1]
      )
      this.mouseDownFlag = true
      this.clickXY = [event.clientX,event.clientY]
    },
    move(event){
      if (this.mouseDownFlag){
        this.moveEl(event.clientX,event.clientY)
      }
    },
    stop(){
      this.mouseDownFlag = false
      this.lastXY = Object.assign({}, this.currentXY)
      if(this.borders.top.show==true){
        this.borders.top.show = false
        this.borders.top.hide = true
      }
      if(this.borders.bottom.show==true){
        this.borders.bottom.show = false
        this.borders.bottom.hide = true
      }
      if(this.borders.left.show==true){
        this.borders.left.show = false
        this.borders.left.hide = true
      }
      if(this.borders.right.show==true){
        this.borders.right.show = false
        this.borders.right.hide = true
      }
    },
    moveEl(mouseX,mouseY){ //изменение координат интерактивного объекта с проверкой максимальных/минимальных значений
      let x = this.lastXY[0] + (mouseX - this.clickXY[0])
      let y = this.lastXY[1] + (mouseY - this.clickXY[1])
      if(x <= this.maximalXY[0] && x >= 0){ 
        this.currentXY[0] = x
        if(this.borders.left.show==true){
          this.borders.left.show = false
          this.borders.left.hide = true
        }
        if(this.borders.right.show==true){
          this.borders.right.show = false
          this.borders.right.hide = true
        }
      }
      else {
        if (x > this.maximalXY[0]){
          this.currentXY[0] = this.maximalXY[0]
          this.borders.right.show = true
          this.borders.right.hide = false
        }
        if (x < 0){
          this.currentXY[0] = 0
          this.borders.left.show = true
          this.borders.left.hide = false
        }
      }
      if(y <= this.maximalXY[1] && y >= 0){
        this.currentXY[1] = y
        if(this.borders.top.show==true){
          this.borders.top.show = false
          this.borders.top.hide = true
        }
        if(this.borders.bottom.show==true){
          this.borders.bottom.show = false
          this.borders.bottom.hide = true
        }
      } 
      else {
        if (y > this.maximalXY[1]){
          this.currentXY[1] = this.maximalXY[1]
          this.borders.bottom.show = true
          this.borders.bottom.hide = false
        }
        if (y < 0){ 
          this.currentXY[1] = 0
          this.borders.top.show = true
          this.borders.top.hide = false
        }
      }
    },
    setSizes(){ //метод перезаписывает значения при изменении размера окна
      this.windowSize = [document.documentElement.clientWidth, document.documentElement.clientHeight]
      this.responsiveElSize = [document.getElementById('responsiveEl').offsetWidth, document.getElementById('responsiveEl').offsetHeight]
      this.currentXY = [this.responsiveElSize[0]/2 - this.interactiveElSize[0]/2, this.responsiveElSize[1]/2 - this.interactiveElSize[1]/2],
      this.maximalXY = [this.responsiveElSize[0] - this.interactiveElSize[0], this.responsiveElSize[1] - this.interactiveElSize[1]]
      this.marginXY = [(this.windowSize[0] - this.responsiveElSize[0])/2, (this.windowSize[1] - this.responsiveElSize[1])/2]
      this.lastXY = Object.assign({}, this.currentXY)
    },
  }
}
</script>

<style scoped>
#responsiveEl{
  background-color: #333;
  width: 80vw;
  height: 80vh;
  min-width: 300px;
  min-height: 300px;
  margin: auto;
  display: flex;
  color: #fff;
  font-size: 40px;
  line-height: 500px;
  position: relative;
  overflow:hidden
}
.fullscreen{
  position: fixed;
  width: 100vw;
  height: 100vh;
  z-index: 999;
}
.borders *{
  position: absolute;
  background: radial-gradient(circle, rgb(255 0 0) 0%, rgba(2,0,36,0) 100%);
  opacity: 0;
  animation-name: none;
  animation-timing-function: cubic-bezier(0.7, 0.2, 0.77, 1.01);
}
.borders *.show{
  animation-name: show;
  animation-duration: 1s;
  animation-fill-mode: forwards;
}
.borders *.hide{
  animation-name: hide;
  animation-duration: 1s;
}
#top{
  width: 100%;
  height: 2px;
}
#bottom{
  bottom: 0;
  width: 100%;
  height: 2px;
}
#left{
  left: 0;
  width: 2px;
  height: 100%;
}
#right{
  right: 0;
  width: 2px;
  height: 100%;
}
@keyframes show {
    from {
        opacity: 100;
    }
    to {
        opacity: 100;
    }
}
@keyframes hide {
    from {
        opacity: 100;
    }
    to {
        opacity: 0;
    }
}
</style>
