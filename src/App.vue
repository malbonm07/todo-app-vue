<template>
  <div id="app">
    <img width="15%" src="./assets/logo.png">
    <!-- <HelloWorld /> -->
    <Todolist/>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld";
import Todolist from "./components/Todolist.vue";

export default {
  name: "App",
  components: {
    HelloWorld,
    Todolist
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
  margin: 80px auto;
  border: 2px solid rgb(223, 214, 214);
  min-width: 300px;
  max-width: 500px;
  min-height: 400px;
  padding: 20px;
  box-shadow: 1px 1px 5px 0px #939697;
}
</style>
