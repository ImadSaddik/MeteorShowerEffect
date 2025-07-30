<template>
  <canvas id="meteor-canvas"></canvas>
</template>

<script>
export default {
  name: "MeteorShowers",
  data() {
    return {
      context: null,
      width: window.innerWidth,
      height: window.innerHeight,
      meteors: [],
      radiant: null,
      animationFrameId: null,
      meteorTimeoutId: null,

      METEOR_SPEED_MIN: 6,
      METEOR_SPEED_MAX: 13,
      METEOR_LENGTH_MIN: 70,
      METEOR_LENGTH_MAX: 220,
      METEOR_LINEWIDTH_MIN: 2,
      METEOR_LINEWIDTH_MAX: 6,
      METEOR_OPACITY_MIN: 0.4,
      METEOR_OPACITY_MAX: 1.0,

      METEOR_FADING_FACTOR: 0.01,
      SPAWN_ZONE_WIDTH_SCALE: 0.2,
      RADIANT_POSITION_Y: -50,

      BURST_CHANCE: 0.2,
      BURST_MIN_COUNT: 2,
      BURST_MAX_COUNT: 4,
      PAUSE_MIN_MS: 100,
      PAUSE_MAX_MS: 1000,
    };
  },
  methods: {
    spawnMeteor() {
      const spawnZoneWidth = this.width * this.SPAWN_ZONE_WIDTH_SCALE;
      const spawnZoneOffset = (this.width - spawnZoneWidth) / 2;

      const startX = Math.random() * spawnZoneWidth + spawnZoneOffset;
      const startY = 0;

      const angle = Math.atan2(startY - this.radiant.positionY, startX - this.radiant.positionX);

      const speed = Math.random() * (this.METEOR_SPEED_MAX - this.METEOR_SPEED_MIN) + this.METEOR_SPEED_MIN;
      const length = Math.random() * (this.METEOR_LENGTH_MAX - this.METEOR_LENGTH_MIN) + this.METEOR_LENGTH_MIN;
      const lineWidth =
        Math.random() * (this.METEOR_LINEWIDTH_MAX - this.METEOR_LINEWIDTH_MIN) + this.METEOR_LINEWIDTH_MIN;
      const opacity = Math.random() * (this.METEOR_OPACITY_MAX - this.METEOR_OPACITY_MIN) + this.METEOR_OPACITY_MIN;

      this.meteors.push({
        positionX: startX,
        positionY: startY,
        stepSizeX: Math.cos(angle) * speed,
        stepSizeY: Math.sin(angle) * speed,
        length,
        lineWidth,
        opacity,
      });
    },
    drawMeteors() {
      this.context.clearRect(0, 0, this.width, this.height);

      for (let i = this.meteors.length - 1; i >= 0; i--) {
        const meteor = this.meteors[i];

        // 1. Get the meteor's current position and angle
        const headX = meteor.positionX;
        const headY = meteor.positionY;
        const angle = Math.atan2(meteor.stepSizeY, meteor.stepSizeX);

        // 2. Calculate the tail's position based on the meteor's length
        const tailX = headX - Math.cos(angle) * meteor.length;
        const tailY = headY - Math.sin(angle) * meteor.length;

        // Apply the meteor's opacity to the whole shape
        this.context.globalAlpha = meteor.opacity;

        // 3. Draw the tail as a filled triangle
        const tailGradient = this.context.createLinearGradient(headX, headY, tailX, tailY);
        tailGradient.addColorStop(0, "rgba(255, 255, 255, 0.6)");
        tailGradient.addColorStop(1, "rgba(255, 255, 255, 0)");

        const perpX = -Math.sin(angle);
        const perpY = Math.cos(angle);

        const headBase1X = headX + (meteor.lineWidth / 2) * perpX;
        const headBase1Y = headY + (meteor.lineWidth / 2) * perpY;
        const headBase2X = headX - (meteor.lineWidth / 2) * perpX;
        const headBase2Y = headY - (meteor.lineWidth / 2) * perpY;

        this.context.beginPath();
        this.context.moveTo(headBase1X, headBase1Y);
        this.context.lineTo(headBase2X, headBase2Y);
        this.context.lineTo(tailX, tailY);
        this.context.closePath();
        this.context.fillStyle = tailGradient;
        this.context.fill();

        // 4. Draw the solid head
        this.context.beginPath();
        this.context.arc(headX, headY, meteor.lineWidth / 2, 0, 2 * Math.PI);
        this.context.fillStyle = "white";
        this.context.fill();
        this.context.globalAlpha = 1.0;

        // Update meteor position and opacity for the next frame
        meteor.positionX += meteor.stepSizeX;
        meteor.positionY += meteor.stepSizeY;
        meteor.opacity -= this.METEOR_FADING_FACTOR;

        const isFadedOut = meteor.opacity <= 0;
        const isOffScreen =
          meteor.positionX > this.width + meteor.length || meteor.positionY > this.height + meteor.length;

        if (isFadedOut || isOffScreen) {
          this.meteors.splice(i, 1);
        }
      }
      this.animationFrameId = requestAnimationFrame(this.drawMeteors);
    },
    handleResize() {
      this.width = window.innerWidth;
      this.height = window.innerHeight;
      this.radiant.positionX = this.width / 2;

      const canvas = this.$el;
      canvas.width = this.width;
      canvas.height = this.height;
    },
    setupCanvas() {
      const canvas = this.$el;
      this.context = canvas.getContext("2d");
      canvas.width = this.width;
      canvas.height = this.height;
    },
    scheduleNextMeteor() {
      this.meteorTimeoutId = setTimeout(
        () => {
          if (Math.random() < this.BURST_CHANCE) {
            const burstCount =
              Math.floor(Math.random() * (this.BURST_MAX_COUNT - this.BURST_MIN_COUNT + 1)) + this.BURST_MIN_COUNT;
            for (let i = 0; i < burstCount; i++) {
              this.spawnMeteor();
            }
          } else {
            this.spawnMeteor();
          }

          this.scheduleNextMeteor();
        },
        Math.random() < 0.5 ? this.PAUSE_MIN_MS : this.PAUSE_MAX_MS
      );
    },
  },
  mounted() {
    this.radiant = { positionX: this.width / 2, positionY: this.RADIANT_POSITION_Y };
    this.setupCanvas();
    this.drawMeteors();
    this.scheduleNextMeteor();
    window.addEventListener("resize", this.handleResize);
  },
  beforeUnmount() {
    window.removeEventListener("resize", this.handleResize);
    cancelAnimationFrame(this.animationFrameId);
    clearTimeout(this.meteorTimeoutId);
  },
};
</script>

<style scoped>
#meteor-canvas {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 0;
  width: 100vw;
  height: 100vh;
  pointer-events: none;
}
</style>
