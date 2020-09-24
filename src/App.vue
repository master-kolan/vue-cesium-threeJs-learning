<template>
  <div id="app">
    <CesiumDemo1 />
  </div>
</template>

<script>
import CesiumDemo1 from './components/ThreeJsExample'

export default {
  name: 'App',
  components: {
    CesiumDemo1
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
