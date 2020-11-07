<template>
  <div>
    <img width="80%" :src="imageSrc" />
    <div>
      <button v-on:click="play()">Start Stream</button>
      <button v-on:click="pause()">Pause Stream</button>
    </div>
  </div>
</template>

<script>
import axios from "axios";


export default {
  props: ["src"],
  data() {
    return {
      imageSrc: this.src,
      interval: "",
    };
  },
  created() {
    // this.imageSrc = this.src;
  },
  methods: {
    play() {
      this.interval = setInterval(() => {
        this.reqImg();
      }, 2000);
    },
    pause() {
      clearInterval(this.interval);
    },
    async reqImg() {
      axios
        .get(this.src, {
          responseType: "arraybuffer",
        })
        .then((response) => {
          this.imageSrc =
            "data:image/jpeg;base64," +
            new Buffer(response.data, "binary").toString("base64");
        });
    }
  }
};
</script>

<style></style>
