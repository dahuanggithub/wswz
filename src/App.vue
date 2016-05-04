<template>
  <div id="app">
  <weather></weather>
  </div>
</template>

<script>
import Weather from './components/Weather'

export default {
  components: {
    Weather
  }
}
</script>

<style>
html {
  height: 100%;
}

*{
  margin: 0;
  padding: 0;
}

#app {
  color: #2c3e50;
  
  font-family: Source Sans Pro, Helvetica, sans-serif;
  text-align: center;
}

#app a {
  color: #42b983;
  text-decoration: none;
}

.logo {
  width: 100px;
  height: 100px
}
</style>
