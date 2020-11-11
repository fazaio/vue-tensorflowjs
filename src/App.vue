<template>
  <div id="app">
    <tfjs src="http://proxy.cctv.malangkota.go.id/image?host=10.0.2.33"/>
  </div>
</template>

<script>
import tfjs from './components/tfjs.vue'
// import tf from './components/tensorflowjs'

export default {
  name: 'App',
  components: {
    tfjs
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
