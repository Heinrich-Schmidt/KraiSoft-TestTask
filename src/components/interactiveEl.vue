<template>
    <div id="interactiveEl" :style="{ width: `${size[0]}px`, height: `${size[1]}px`, left: `${position[0]}px`, top: `${position[1]}px`, backgroundColor: `hsl(${position[0]-position[1]}, 70%, 60%)`}"></div>
</template>
  
<script>
  export default {
    name: 'InteractiveEl',
    props: {
        position: Array[Number],
        size: Array[Number],
    }
    }
</script>
<style scoped>
#interactiveEl{
    position: absolute;
    cursor: pointer;
    z-index: 99;
}
</style>