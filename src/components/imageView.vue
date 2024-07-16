<template>
    <div>
      <input type="file" @change="onFileChange" />
      <canvas ref="canvas" :width="canvasWidth" :height="canvasHeight"></canvas>
    </div>
  </template>
  
  <script>
  export default {
    name: "CanvasImage",
    data() {
      return {
        canvasWidth: 500,
        canvasHeight: 500,
      };
    },
    methods: {
      onFileChange(event) {
        const file = event.target.files[0];
        if (file) {
          const reader = new FileReader();
          reader.onload = (e) => {
            const image = new Image();
            image.src = e.target.result;
            image.onload = () => {
              this.drawImage(image);
            };
          };
          reader.readAsDataURL(file);
        }
      },
      drawImage(image) {
        const canvas = this.$refs.canvas;
        const ctx = canvas.getContext("2d");
        ctx.clearRect(0, 0, this.canvasWidth, this.canvasHeight); // Clear the canvas
        ctx.drawImage(image, 0, 0, this.canvasWidth, this.canvasHeight);
      },
    },
  };
  </script>
  
  <style>
  /* Add any styles if needed */
  </style>
  