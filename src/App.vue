<template>
  <MeteorShowers />
  <!-- <CometDesigner /> -->
  <!-- <AnimatedComet /> -->
</template>

<script>
import MeteorShowers from "./components/MeteorShowers.vue";
// import CometDesigner from "./components/CometDesigner.vue";
// import AnimatedComet from "./components/AnimatedComet.vue";

export default {
  name: "App",
  components: {
    MeteorShowers,
    // CometDesigner,
    // AnimatedComet,
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
