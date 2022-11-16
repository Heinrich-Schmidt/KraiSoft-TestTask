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
    <div id="interactiveEl" :style="{ left: `${currentXY[0]}px`, top: `${currentXY[1]}px`, backgroundColor: `hsl(${currentXY[0]-currentXY[1]}, 70%, 60%)`}"></div>
  </div>
</template>

<script>
export default {
  name: 'ResponsiveEl',
  data() {
    return {
      currentXY: [0,0],
      lastXY: [0,0],
      maximalXY:[0,0],
      mouseXY:[0,0],
      mouseDownFlag: false,   
      interactiveElSize: [0,0], 
      responsiveElSize: [0,0],
      windowSize: [0,0],
      marginXY: [0,0],
    }
  },
  
  mounted() {
    this.setSizes()
  },
  methods: {

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
      moveEl(mouseX,mouseY){
        let x = this.lastXY[0] + (mouseX - this.mouseXY[0])
        let y = this.lastXY[1] + (mouseY - this.mouseXY[1])
        console.log(x + " " + this.maximalXY[0])
        if(x <= this.maximalXY[0] && x >= 0) this.currentXY[0] = x
        else {
          if (x > this.maximalXY[0]) this.currentXY[0] = this.maximalXY[0]
          if (x < 0) this.currentXY[0] = 0
        }
        if(y <= this.maximalXY[1] && y >= 0) this.currentXY[1] = y
        else {
          if (y > this.maximalXY[1]) this.currentXY[1] = this.maximalXY[1]
          if (y < 0) this.currentXY[1] = 0
        }
      },
      start(event){
        if(
        event.clientX - this.marginXY[0] >= this.currentXY[0] && 
        event.clientX - this.marginXY[0] <= this.currentXY[0]+this.interactiveElSize[0] &&
        event.clientY - this.marginXY[1] >= this.currentXY[1] &&
        event.clientY - this.marginXY[1] <= this.currentXY[1]+this.interactiveElSize[1]
        )
        this.mouseDownFlag = true
        this.mouseXY = [event.clientX,event.clientY]
        console.log(event.clientX - this.marginXY[0])
      },
      move(event){
        if (this.mouseDownFlag){
          this.moveEl(event.clientX,event.clientY)
          
        }

      },
      stop(){
        this.mouseDownFlag = false
        this.lastXY = Object.assign({}, this.currentXY)
      },
      setSizes(){
        this.windowSize = [document.documentElement.clientWidth, document.documentElement.clientHeight]
        this.interactiveElSize = [document.getElementById('interactiveEl').offsetWidth, document.getElementById('interactiveEl').offsetHeight]
        this.responsiveElSize = [document.getElementById('responsiveEl').offsetWidth, document.getElementById('responsiveEl').offsetHeight]
        this.maximalXY = [this.responsiveElSize[0] - this.interactiveElSize[0], this.responsiveElSize[1] - this.interactiveElSize[1]]
        this.marginXY = [(this.windowSize[0] - this.responsiveElSize[0])/2, (this.windowSize[1] - this.responsiveElSize[1])/2]
        console.log(document.documentElement.clientHeight)
      },
  }
}

</script>

<style scoped>
#responsiveEl{
  background-color: #333;
  width: 80vw;
  height: 80vh;
  margin: auto;
  display: flex;
  color: #fff;
  font-size: 40px;
  line-height: 500px;
  position: relative;
  overflow:hidden
}
#interactiveEl{
background-color: #666;
width: 20vw;
height: 20vw;
max-width: 200px;
max-height: 200px;
min-width: 100px;
min-height: 100px;
margin: auto;
position: absolute;
cursor: pointer;
transition: background-color 0.2s ease;
}
.fullscreen{
  position: fixed;
  width: 100vw;
  height: 100vh;
  z-index: 999;
}
</style>
