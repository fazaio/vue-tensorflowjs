<template>
  <div class="hello">
    <img width="100%" src="http://proxy.cctv.malangkota.go.id/image?host=10.0.3.35" alt="">
    <h1>{{ msg }}</h1>
    <p>{{ ekse }}</p>
    <p>{{ data }}</p>
    <button v-on:click="getImg()">ekse</button>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data(){
    return {
      data: '',
      ekse: ''
    }
  },
  methods: {
    getImg(){
      this.ekse = 'gas!'
      axios.get('http://proxy.cctv.malangkota.go.id/image?host=10.0.3.35').then((r) => {
        this.data = r
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
