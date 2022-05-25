<template>
  <div class="tile">{{ msg }}</div>
</template>

<script>
export default {
  name: 'TileComponent',
  props: {
    msg: Number
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.tile {
  height: 2em;
  width: 2em;
  background-color: hotpink;
  flex-shrink: 0;
}
</style>
