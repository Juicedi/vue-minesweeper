<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
  </div>
  <div class="playarea" :style="cssProps">
    <Tile v-for="item in items" :key="item.id" :msg="item.id"/>
  </div>
</template>

<script>
import Tile from './Tile.vue';

export default {
  name: 'HelloWorld',

  props: {
    msg: String,
    numberOfTiles: Number
  },

  components: {
    Tile
  },

  data() {
    return {
      items: (() => {
        const result = [];
        let counter = 0;

        for (let i = 0; i < this.numberOfTiles; i++) {
          for (let j = 0; j < this.numberOfTiles; j++) {
            counter++;
            result.push({ id: counter });
          }
        }

        return result;
      })()
    }
  },

  computed: {
    cssProps() {
      return {
        '--size': (this.numberOfTiles * 2) + "em",
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.playarea {
  background-color: #eee;
  height: var(--size);
  width: var(--size);
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-content: flex-start;
}
</style>
