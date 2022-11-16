<template>
  <div id="responsiveEl">
    <InteractiveEl/>
  </div>
</template>

<script>
import InteractiveEl from '../components/interactiveEl.vue'

export default {
  name: 'ResponsiveEl',
  components: {
    InteractiveEl
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
}
</style>
