<template>
  <div id="app">
    <imeg src="http://proxy.cctv.malangkota.go.id/image?host=10.0.3.54"/>
  </div>
</template>

<script>
import imeg from './components/reloadImg.vue'
// import tf from './components/tensorflowjs'

export default {
  name: 'App',
  components: {
    imeg,
    // tf
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
