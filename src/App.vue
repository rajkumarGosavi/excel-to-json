<template>
  <div id="app">
    <Excel></Excel>
  </div>
</template>

<script>
import Excel from "../src/components/Excel";
export default {
  name: "app",
  components: {
    Excel
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
