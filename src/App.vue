<template>
  <HeaderComponent />
  <BodyComponent />
</template>

<script>
import HeaderComponent from "./components/HeaderComponent.vue";
import BodyComponent from "./components/BodyComponent.vue";

export default {
  name: "App",
  components: {
    HeaderComponent,
    BodyComponent,
  },
};
</script>

<style>
*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  position: relative;
  top: 0;
  width: 406px;
  height: 732px;
  margin: auto;
  border: #2c3e50 12px solid;
  box-sizing: border-box;
  border-radius: 18px;
  overflow: scroll;
  overflow-x: hidden;
}

#app::-webkit-scrollbar {
  width: 0px;
}
</style>
