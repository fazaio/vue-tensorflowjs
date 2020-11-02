<template>
  <img width="80%" :src="imageSrc">
</template>

<script>
export default {
  props: ['src'],
  data() {
    return {
      show: false,
      interval: null,
      imageSrc: null
    }
  },
  created() {
    this.imageSrc = this.$props.src;
    console.log(this.imageSrc);
    this.renderImage();
  },
  mounted() {
    this.startInterval();
  },
  beforeDestroy() {
    // console.log('beforeDestroy');
    clearInterval(this.interval);
  },
  destroyed() {
    // console.log('destroyed');
    clearInterval(this.interval);
  },
  methods: {
    startInterval() {
      this.interval = setInterval(() => {
        this.renderImage();
      }, 1000)
    },
    toDataURL(url, callback) {
      var xhr = new XMLHttpRequest();
      xhr.onload = function() {
        var reader = new FileReader();
        reader.onloadend = function() {
          callback(reader.result);
        }
        reader.readAsDataURL(xhr.response);
      };
      xhr.open('GET', url);
      xhr.responseType = 'blob';
      xhr.send();
    },
    renderImage() {
      this.toDataURL(this.$props.src, (res) => {
        // console.log(res);
        this.imageSrc = res;
      })
    },
  }
}
</script>

<style>

</style>