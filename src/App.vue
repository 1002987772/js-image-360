<template>
  <div id="app">
    <view-360></view-360>
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
  </div>
</template>

<script>
import View360 from './components/View360.vue'

export default {
  name: 'App',
  components: {
    View360
  }
}
</script>

<style>
*{
  margin: 0;
  padding: 0;
  outline: none;
  appearance: none;
  -webkit-tap-highlight-color: transparent;
}

html, body{
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  width: 100%;
  height: 100%;
  position: relative;
}
</style>
