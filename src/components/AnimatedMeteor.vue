<template>
  <div>
    <canvas id="animated-meteor-canvas"></canvas>
    <div class="instructions">Press Enter to move the meteor</div>
  </div>
</template>

<script>
export default {
  name: "AnimatedMeteor",
  data() {
    return {
      context: null,
      width: window.innerWidth,
      height: window.innerHeight,
      scale: 3,
      meteor: {
        positionX: 150,
        positionY: 150,
        stepSizeX: 10,
        stepSizeY: 10,
        length: 50,
        lineWidth: 10,
        opacity: 1,
      },
    };
  },
  methods: {
    setupCanvas() {
      const canvas = this.$el.querySelector("#animated-meteor-canvas");
      this.context = canvas.getContext("2d");
      canvas.width = this.width;
      canvas.height = this.height;
    },

    stepAnimation() {
      this.meteor.positionX += this.meteor.stepSizeX;
      this.meteor.positionY += this.meteor.stepSizeY;

      this.drawMeteor();
    },

    handleKeyPress(event) {
      if (event.key === "Enter") {
        this.stepAnimation();
      }
    },

    drawMeteor() {
      // Clear the entire canvas before drawing
      this.context.clearRect(0, 0, this.width, this.height);

      // Calculate the center, head, and tail positions
      const scaledLength = this.meteor.length * this.scale;
      const scaledLineWidth = this.meteor.lineWidth * this.scale;

      const headX = this.meteor.positionX;
      const headY = this.meteor.positionY;
      const angle = Math.atan2(this.meteor.stepSizeY, this.meteor.stepSizeX);

      const tailX = headX - Math.cos(angle) * scaledLength;
      const tailY = headY - Math.sin(angle) * scaledLength;

      this.context.globalAlpha = this.meteor.opacity;

      // Draw the tail
      const tailGradient = this.context.createLinearGradient(headX, headY, tailX, tailY);
      tailGradient.addColorStop(0, "rgba(255, 255, 255, 0.6)");
      tailGradient.addColorStop(1, "rgba(255, 255, 255, 0)");

      const perpX = -Math.sin(angle);
      const perpY = Math.cos(angle);
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

      // Draw the solid head
      this.context.beginPath();
      this.context.arc(headX, headY, scaledLineWidth / 2, 0, 2 * Math.PI);
      this.context.fillStyle = "white";
      this.context.fill();

      this.context.globalAlpha = 1.0;
    },
  },
  mounted() {
    this.setupCanvas();
    this.drawMeteor();

    window.addEventListener("keydown", this.handleKeyPress);
  },
  beforeUnmount() {
    window.removeEventListener("keydown", this.handleKeyPress);
  },
};
</script>

<style scoped>
#animated-meteor-canvas {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 0;
  width: 100vw;
  height: 100vh;
  pointer-events: none;
  background-color: #01030d;
}

.instructions {
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  color: white;
  background-color: rgba(0, 0, 0, 0.5);
  padding: 10px 20px;
  border-radius: 8px;
  font-family: sans-serif;
  font-size: 16px;
}
</style>
