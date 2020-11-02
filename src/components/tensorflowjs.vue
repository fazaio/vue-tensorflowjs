<template>
  <div>
    <h1>hello world</h1>
    <img
      id="img"
      src="https://images.pexels.com/photos/4613887/pexels-photo-4613887.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
    />
  </div>
</template>

<script>
require('@tensorflow/tfjs-backend-cpu');
require('@tensorflow/tfjs-backend-webgl');
const tfjs = require('@tensorflow-models/coco-ssd');

export default {
  mounted() {
    const img = document.getElementById("img");

    // Load the model.
    tfjs.load().then((model) => {
      // detect objects in the image.
      model.detect(img).then((predictions) => {
        console.log("Predictions: ", predictions);
      });
    });
  },
};
</script>

<style></style>
