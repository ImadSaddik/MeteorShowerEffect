<template>
  <MeteorShowers />
  <!-- <MeteorDesigner /> -->
  <!-- <AnimatedMeteor /> -->
</template>

<script>
import MeteorShowers from "./components/MeteorShowers.vue";
// import MeteorDesigner from "./components/MeteorDesigner.vue";
// import AnimatedMeteor from "./components/AnimatedMeteor.vue";

export default {
  name: "App",
  components: {
    MeteorShowers,
    // MeteorDesigner,
    // AnimatedMeteor,
  },
};
</script>

<style>
#app {
  font-family: "Inter", Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;

  width: 100%;
}

body {
  margin: 0;
  padding: 0;
  background: #01030e;
}
</style>
