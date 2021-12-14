<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
  <demo/>
</template>

<script>
import 'bulma/css/bulma.css'
// import HelloWorld from './components/HelloWorld.vue'
import Demo from './components/Demo.vue'
export default {
  name: 'App',
  components: {
    // HelloWorld
    Demo
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
