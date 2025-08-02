<template>
  <canvas id="comet-canvas"></canvas>
</template>

<script>
export default {
  name: "CometDesigner",
  data() {
    return {
      context: null,
      width: window.innerWidth,
      height: window.innerHeight,
      scale: 3,
      comet: {
        length: 250,
        lineWidth: 50,
        opacity: 1,
        angle: Math.PI / 0.5,
      },
    };
  },
  watch: {
    comet: {
      handler() {
        this.drawComet();
      },
      deep: true,
    },
    scale() {
      this.drawComet();
    },
  },
  methods: {
    setupCanvas() {
      const canvas = this.$el;
      this.context = canvas.getContext("2d");
      canvas.width = this.width;
      canvas.height = this.height;
    },

    drawComet() {
      // Clear the entire canvas before drawing
      this.context.clearRect(0, 0, this.width, this.height);

      // Calculate the center, head, and tail positions
      const centerX = this.width / 2;
      const centerY = this.height / 2;

      const scaledLength = this.comet.length * this.scale;
      const scaledLineWidth = this.comet.lineWidth * this.scale;

      const headX = centerX + (scaledLength / 2) * Math.cos(this.comet.angle);
      const headY = centerY + (scaledLength / 2) * Math.sin(this.comet.angle);

      const tailX = centerX - (scaledLength / 2) * Math.cos(this.comet.angle);
      const tailY = centerY - (scaledLength / 2) * Math.sin(this.comet.angle);

      // Draw the tail
      const tailGradient = this.context.createLinearGradient(headX, headY, tailX, tailY);
      tailGradient.addColorStop(0, "rgba(255, 255, 255, 0.6)");
      tailGradient.addColorStop(1, "rgba(255, 255, 255, 0)");

      const perpX = -Math.sin(this.comet.angle);
      const perpY = Math.cos(this.comet.angle);

      const headBase1X = headX + (scaledLineWidth / 2) * perpX;
      const headBase1Y = headY + (scaledLineWidth / 2) * perpY;
      const headBase2X = headX - (scaledLineWidth / 2) * perpX;
      const headBase2Y = headY - (scaledLineWidth / 2) * perpY;

      this.context.beginPath();
      this.context.moveTo(headBase1X, headBase1Y);
      this.context.lineTo(headBase2X, headBase2Y);
      this.context.lineTo(tailX, tailY);
      this.context.closePath();
      this.context.fillStyle = tailGradient;
      this.context.fill();

      // Draw a solid head
      this.context.beginPath();
      this.context.arc(headX, headY, scaledLineWidth / 2, 0, 2 * Math.PI);

      this.context.fillStyle = "white";
      this.context.fill();
    },

    handleResize() {
      this.width = window.innerWidth;
      this.height = window.innerHeight;
      const canvas = this.$el;
      canvas.width = this.width;
      canvas.height = this.height;
      this.drawComet();
    },
  },
  mounted() {
    this.setupCanvas();
    this.drawComet();
    window.addEventListener("resize", this.handleResize);
  },
  beforeUnmount() {
    window.removeEventListener("resize", this.handleResize);
  },
};
</script>

<style scoped>
#comet-canvas {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 0;
  width: 100vw;
  height: 100vh;
  pointer-events: none;
  background-color: #01030d;
}
</style>
