<template>
  <input type="number" name="playarea-size" id="playarea-size-input" v-model="numberOfTiles" @input="resetPlayarea">
  <PlayareaComponent :numberOfTiles="numberOfTiles" :minePercent="minePercent" :debug="isDebugOn" ref="playarea" />
</template>

<script>
import PlayareaComponent from './components/PlayareaComponent.vue'

export default {
  name: 'App',
  components: {
    PlayareaComponent
  },
  data() {
    return {
      numberOfTiles: 10,
      minePercent: 0.8,
      isDebugOn: false,
    }
  },
  methods: {
    resetPlayarea() {
      this.$refs.playarea.reset();
    }
  }
}
</script>

<style>
:root {
  --defaultTextColor: #2c3e50;
  --closedBg: darkgrey;
  --openedBg: white;
  --tileTextColor: black;
  --noMinesBg: #ddf;
  --mineBg: black;
  --markedBg: hotpink;
}

html {
  font-size: 16px;
}

#app {
  font-family: Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: var(--defaultTextColor);
  margin-top: 4rem;
}
</style>
