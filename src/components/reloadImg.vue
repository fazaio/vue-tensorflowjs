<template>
  <div>
    <img width="800px" :src="imageSrc" />
    <div>
      <button v-on:click="play()">Start Stream</button>
      <button v-on:click="pause()">Pause Stream</button>
    </div>
    <div>
      <canvas id="canvas"></canvas>
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
      this.interval = setInterval(() => {
        this.refresh();
      }, 10000);
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
            let ima =
              "data:image/jpeg;base64," +
              new Buffer(response.data, "binary").toString("base64");
            this.imageSrc = ima;
            // this.canvas(ima)
            const im = new Image();
            im.crossOrigin = "anonymous";
            im.src = ima;
            im.width = 600;
            im.height = 388;
            im.onload = () => {
              resolve(im);
            };
          });
      });
    },
    async tfjs() {
      console.log("Star Predict!");
      const img = await this.createImg();
      const model = await cocoSsd.load({ base: "lite_mobilenet_v2" });
      const predictions = await model.detect(img);
      return new Promise((resolve) => {
        return resolve([img, predictions]);
      });
    },
    refresh() {
      const tfjs = this.tfjs();
      tfjs.then((res) => {
        this.canvas(res[0], res[1]);
      });
    },
    canvas(img, predictions) {
      // console.log(img)
      console.log(predictions)
      const canvas = document.getElementById("canvas");
      const ctx = canvas.getContext("2d");
      // draw something on canvas
      canvas.width = img.width;
      canvas.height = img.height;
      ctx.clearRect(0, 0, ctx.canvas.width, ctx.canvas.height); // Fonts
      const font = "16px sans-serif";
      ctx.font = font;
      ctx.textBaseline = "top";
      ctx.drawImage(img, 0, 0, img.width, img.height);
      predictions.forEach((prediction) => {
        const x = prediction.bbox[0];
        const y = prediction.bbox[1];
        const width = prediction.bbox[2];
        const height = prediction.bbox[3]; // Bounding box
        ctx.strokeStyle = "#00FFFF";
        ctx.lineWidth = 2;
        ctx.strokeRect(x, y, width, height); // Label background
        ctx.fillStyle = "#00FFFF";
        const textWidth = ctx.measureText(prediction.class).width;
        const textHeight = parseInt(font, 10); // base 10
        ctx.fillRect(x, y, textWidth + 4, textHeight + 4);
      });
      predictions.forEach((prediction) => {
        const x = prediction.bbox[0];
        const y = prediction.bbox[1];
        ctx.fillStyle = "#000000";
        ctx.fillText(prediction.class, x, y);
      });
    },
  },
};
</script>

<style></style>
