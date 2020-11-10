<template>
  <div>
    <div>
      <button v-on:click="play()">Get condition</button>
      <button v-on:click="pause()">exit</button>
      <div style="text-align: center;color:red;">
        {{ debugStatus }} - {{ status }}
      </div>
    </div>
    <img v-if="show" width="600px" :src="imageSrc" />
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
      modelPromise: null,
      model: null,
      imageSrc: this.src,
      interval: "",
      show: true,
      debugStatus: "",
      status: "",
      isModelReady: "",
    };
  },
  methods: {
    play() {
      this.main();
      this.show = false;
      this.debugStatus = "Playing";
    },
    pause() {
      clearInterval(this.interval);
      this.debugStatus = "Stoping";
    },
    createImg() {
      return new Promise((resolve) => {
        axios
          .get(this.src, {
            responseType: "arraybuffer",
          })
          .then((response) => {
            // console.log("response from axios");
            this.status = "get base64!";
            let ima =
              "data:image/jpeg;base64," +
              new Buffer(response.data, "binary").toString("base64");
            // this.imageSrc = ima;
            // this.canvas(ima)
            const im = new Image();
            im.crossOrigin = "anonymous";
            im.src = ima;
            im.width = 600;
            im.height = 388;
            im.onload = () => {
              resolve(im);
            };
          })
          .catch((err) => {
            console.log("failed to load the model", err);
            throw err;
          });
      });
    },
    loadModel() {
      this.isModelReady = false;
      console.log("loading model!");
      return new Promise((resolve) => {
        cocoSsd
          .load({ base: "mobilenet_v2" })
          .then((model) => {
            // this.model = model;
            resolve(model);
            this.isModelReady = true;
            console.log("model loaded");
          })
          .catch((error) => {
            console.log("failed to load the model", error);
            throw error;
          });
      });
    },
    async detect(img, models) {
      try {
        if (!this.isModelReady) return;
        const predictions = await models.detect(img);
        const newimage = await this.createImg();
        this.canvas(img, predictions);
        requestAnimationFrame(() => {
          console.log("request animation frame");
          this.detect(newimage, models);
        });
      } catch (e) {
        console.log(e);
      }
    },
    main() {
      const imgPromise = this.createImg();
      const modelPromise = this.loadModel();
      Promise.all([imgPromise, modelPromise]).then((res) => {
        this.status = "allPromise Done!";
        this.detect(res[0], res[1]);
      });
    },
    canvas(img, predictions) {
      // console.log(img)
      // console.log(predictions)
      this.status = "render canvas";
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
