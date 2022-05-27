<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
  </div>
  <div class="playarea-wrapper">
    <div class="playarea" :style="cssProps">
      <Tile
        v-for="(tile, i) in tiles.flat()"
        :key="i"
        @click.left="open(tile)"
        @click.right.prevent="mark(tile)"
        :tileData="tile" />
    </div>
  </div>
</template>

<script>
import Tile from './Tile.vue';

const states = {
  active: 'playarea-active',
  end: 'playarea-stop',
}

const tileBorderWidth = 0.1;

states.default = states.active;

function generateTiles() {
  const result = [];
  let counter = 0;
  let isMine;

  function determineSiblingMineCount(tiles, coordinates, size) {
    let siblings = 0;
    let y = Math.max((coordinates.y - 1), 0);
    const endY = Math.min((coordinates.y + 1), (tiles.length - 1));
    let startX = Math.max((coordinates.x - 1), 0);
    const endX = Math.min((coordinates.x + 1), (size - 1));

    for (y; y <= endY; y++) {
      for (let x = startX; x <= endX; x++) {
        if (tiles[y][x].isMine) {
          siblings++;
        }
      }
    }

    return siblings;
  }

  for (let i = 0; i < this.numberOfTiles; i++) {
    result.push([]);

    for (let j = 0; j < this.numberOfTiles; j++) {
      counter++;
      isMine = Math.random() > this.minePercent;

      result[i].push({
        id: counter,
        isMine: isMine,
        isOpen: false,
        isMarked: false,
        borderWidth: tileBorderWidth,
        siblingMines: isMine ? -1 : 0,
        coordinates: { x: j, y: i },
        text: '',
        debug: this.debug,
      });
    }
  }

  if (this.debug) {
    let string;

    for (let y = 0; y < result.length; y++) {
      string = '';

      for (let x = 0; x < result.length; x++) {
        if (result[y][x].isMine) {
          string += 'x ';
        } else {
          string += '0 ';
        }
      }

      console.debug(string);
    }
  }

  for (let y = 0; y < result.length; y++) {
    for (let x = 0; x < result.length; x++) {
      const tile = result[y][x];

      console.assert(typeof tile !== 'undefined', `undefined tile at: x=${x} y=${y}`);

      if (!tile.isMine) {
        tile.siblingMines = determineSiblingMineCount(result, { x, y }, this.numberOfTiles);
      }
    }
  }

  return result;
}

export default {
  name: 'PlayareaComponent',

  props: {
    numberOfTiles: Number,
    minePercent: Number,
    debug: Boolean
  },

  components: {
    Tile
  },

  data() {
    return {
      msg: 'Click squares and try not to click on any mines',
      state: states.default,
      tiles: generateTiles.call(this)
    }
  },

  methods: {
    isActive: function isActive() {
      return this.state === states.active;
    },
    afterTileOpen: function afterTileOpen(tile) {
      if (tile.isMine) {
        this.state = states.end;
        this.msg = 'You have found a mine :)';
        return;
      }

      if (tile.siblingMines === 0) {
        console.log('should show lonely tiles');
        console.log(tile.coordinates);
        // TODO: "Click" on all tiles around the original tile
        // this.open(this.tiles[0][0]);
        return;
      }
    },
    open: function openTile(tile) {
      if (tile.isOpen || tile.isMarked) {
        return;
      }

      tile.text = tile.isMine ? '' : tile.siblingMines;
      tile.isOpened = true;
      this.afterTileOpen(tile);
    },
    mark: function openTile(tile) {
      if (tile.isOpened) {
        return;
      }

      tile.isMarked = !tile.isMarked;
    },
  },

  computed: {
    cssProps() {
      return {
        '--size': (this.numberOfTiles * 2) + (this.numberOfTiles * 2 * tileBorderWidth) + "em",
        '--borderWidth': `${tileBorderWidth}em`,
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 2.5em 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 0.75em;
}

a {
  color: #42b983;
}

.playarea-wrapper {
  display: inline-block;
}

.playarea {
  background-color: black;
  height: var(--size);
  width: var(--size);
  padding: var(--borderWidth);
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-content: flex-start;
}
</style>
