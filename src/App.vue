<template>
  <div id="app">
    <GameComponent />
  </div>
</template>

<script>
import GameComponent from './components/GameComponent.vue'

export default {
  name: 'App',
  components: {
    GameComponent
  }
}
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
}
html, body {
    width: 100%;
    height: 100%;
}
body {
    height: 100%;
    background-color: rgb(231, 181, 181);
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  height: 100%;
  color: #2c3e50;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
