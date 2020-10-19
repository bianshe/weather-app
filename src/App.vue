<template>
  <div id="app">
    <Weather/>
  </div>
</template>

<script>
import Weather from './components/Weather.vue'

export default {
  name: 'App',
  components: {
    Weather
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Source+Sans+Pro&display=swap');
#app {
  font-family: 'Source Sans Pro', sans-serif;
  text-align: center;
  color: #2c3e50;
}
</style>
