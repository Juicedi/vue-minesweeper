<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <PlayareaComponent :numberOfTiles="10" :minePercent="0.8" :debug="true" />
</template>

<script>
import PlayareaComponent from './components/PlayareaComponent.vue'

export default {
  name: 'App',
  components: {
    PlayareaComponent
  }
}
</script>

<style>
:root {
  --closedBg: darkgrey;
  --openedBg: white;
  --tileTextColor: black;
  --noMinesBg: #ddf;
  --mineBg: black;
  --markedBg: hotpink;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  font-size: 16px;
}
</style>
