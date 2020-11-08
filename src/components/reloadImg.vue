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
require("@tensorflow/tfjs-backend-cpu");
require("@tensorflow/tfjs-backend-webgl");
const cocoSsd = require("@tensorflow-models/coco-ssd");

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
      // this.interval = setInterval(() => {
      //   this.tfjs();
      // }, 10000);
      this.tfjs()
    },
    pause() {
      clearInterval(this.interval);
    },
    async createImg() {
      return new Promise((resolve) => {
        axios
          .get(this.src, {
            responseType: "arraybuffer",
          })
          .then((response) => {
            console.log("response from axios");
            let ima = "data:image/jpeg;base64," + new Buffer(response.data, "binary").toString("base64");
            this.imageSrc = ima
            const im = new Image();
            im.crossOrigin = "anonymous";
            im.src = ima;
            im.onload = () => {
              resolve(im);
            };
          });
      });
    },
    async tfjs() {
      console.log("Star Predict!");
      const img = await this.createImg()
      const model = await cocoSsd.load();
      const predictions = await model.detect(img);
      console.log(predictions);
    },
  },
};
</script>

<style></style>
