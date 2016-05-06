<template>
  <div id="app">
  <weather></weather>
  <todo></todo>
  </div>
</template>

<script>
import Weather from './components/Weather'
import Todo from './components/Todo'

export default {
  components: {
    Weather,Todo
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
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  outline: none;
  -webkit-tap-highlight-color: transparent; /* 触摸点击元素背景透明 */
}
*:not(input,textarea) { 
-webkit-touch-callout: none; 
  -webkit-user-select: none; 
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
