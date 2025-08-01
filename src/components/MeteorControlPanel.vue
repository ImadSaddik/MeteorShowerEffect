<template>
  <div class="meteor-control-panel">
    <h2 class="control-title">Meteor controls</h2>
    <div v-for="(value, key) in localConfig" :key="key" class="control-row">
      <div class="control-label">{{ formatLabel(key) }}</div>
      <div class="control-slider">
        <input
          type="range"
          :id="key + '-slider'"
          :min="getMin(key)"
          :max="getMax(key)"
          :step="getStep(key)"
          v-model.number="localConfig[key]"
          @input="emitUpdate"
        />
      </div>
      <div class="control-value">
        <input
          type="number"
          :id="key + '-number'"
          :min="getMin(key)"
          :max="getMax(key)"
          :step="getStep(key)"
          v-model="localConfig[key]"
          @input="onNumberInput(key, $event)"
        />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "MeteorControlPanel",
  props: {
    config: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      localConfig: { ...this.config },
      minMax: {
        METEOR_SPEED_MIN: [1, 30],
        METEOR_SPEED_MAX: [1, 30],
        METEOR_LENGTH_MIN: [50, 500],
        METEOR_LENGTH_MAX: [50, 500],
        METEOR_LINEWIDTH_MIN: [1, 10],
        METEOR_LINEWIDTH_MAX: [1, 10],
        METEOR_OPACITY_MIN: [0, 1],
        METEOR_OPACITY_MAX: [0, 1],
        METEOR_FADING_FACTOR: [0.001, 0.1],
        SPAWN_ZONE_WIDTH_SCALE: [0.01, 1],
        RADIANT_POSITION_Y: [-500, -50],
        BURST_CHANCE: [0, 1],
        BURST_MIN_COUNT: [1, 5],
        BURST_MAX_COUNT: [1, 5],
        PAUSE_MIN_MS: [100, 10000],
        PAUSE_MAX_MS: [100, 10000],
      },
      step: {
        METEOR_OPACITY_MIN: 0.01,
        METEOR_OPACITY_MAX: 0.01,
        METEOR_FADING_FACTOR: 0.001,
        SPAWN_ZONE_WIDTH_SCALE: 0.01,
        BURST_CHANCE: 0.01,
        default: 1,
      },
    };
  },
  watch: {
    config: {
      handler(newVal) {
        this.localConfig = { ...newVal };
      },
      deep: true,
    },
  },
  methods: {
    emitUpdate() {
      this.$emit("update:config", { ...this.localConfig });
    },
    onNumberInput(key, event) {
      const value = event.target.value;
      if (value === "") {
        this.localConfig[key] = this.getMin(key);
      } else {
        let number = Number(value);
        const min = this.getMin(key);
        const max = this.getMax(key);
        if (number < min) number = min;
        if (number > max) number = max;
        this.localConfig[key] = number;
      }
      this.emitUpdate();
    },
    isNumber(value) {
      return typeof value === "number";
    },
    getMin(key) {
      return this.minMax[key][0];
    },
    getMax(key) {
      return this.minMax[key][1];
    },
    getStep(key) {
      return this.step[key] || this.step.default;
    },
    formatLabel(key) {
      const words = key.toLowerCase().split("_");
      if (!words.length) return key;
      words[0] = words[0].charAt(0).toUpperCase() + words[0].slice(1);
      return words.join(" ");
    },
  },
};
</script>

<style scoped>
.meteor-control-panel {
  position: fixed;
  bottom: 24px;
  left: 24px;
  background: rgba(30, 30, 30, 1);
  color: white;
  padding: 16px 40px 0 24px;
  border-radius: 10px;
  z-index: 10;
  font-size: 14px;
}

.control-title {
  margin-top: 0;
  margin-bottom: 20px;
}

.control-row {
  display: grid;
  grid-template-columns: 1.1fr 1.5fr 0.4fr;
  align-items: center;
  margin-bottom: 16px;
  gap: 20px;
}

.control-label {
  text-transform: none;
  font-weight: 500;
  color: #e0e0e0;
}

.control-slider input[type="range"] {
  width: 100%;
  margin: 0;
}

.control-value input[type="number"] {
  width: 100%;
  height: 24px;
  padding: 0 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: white;
  color: black;
}
</style>
