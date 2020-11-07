<template>
  <div>
    <img
      id="img"
      src="@/assets/tes.jpeg"
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
