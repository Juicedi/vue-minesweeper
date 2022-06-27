<template>
  <button type="button" @click="toggleDebug">Toggle debug</button>

  <h1>{{ msg }}</h1>

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
  inactive: 'playarea-inactive',
};

const messages = {
  default: 'Click squares and try not to click on any mines',
  mined: 'You have found a mine :)',
  win: 'The winner is you!',
};

const tileBorderWidth = 0.1;

states.default = states.active;

function getMinCoordinates(coordinates) {
  return {
    yMin: Math.max((coordinates.y - 1), 0),
    xMin: Math.max((coordinates.x - 1), 0)
  };
}

function getMaxCoordinates(coordinates, size) {
  const maxRowIndex = size - 1;

  return {
    yMax: Math.min((coordinates.y + 1), maxRowIndex),
    xMax: Math.min((coordinates.x + 1), maxRowIndex)
  };
}

function generateTiles() {
  const result = [];
  let counter = 0;

  function determineSiblingMineCount(tiles, coordinates, size) {
    let siblings = 0;

    const { xMin, yMin } = getMinCoordinates(coordinates);
    const { xMax, yMax } = getMaxCoordinates(coordinates, size);

    for (let y = yMin; y <= yMax; y++) {
      for (let x = xMin; x <= xMax; x++) {
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
      const isMine = Math.random() > this.minePercent;

      const coordinates = {
        x: j,
        y: i
      };

      const tileData = {
        id: counter,
        isOpened: false,
        isMarked: false,
        isMine: isMine,
        borderWidth: tileBorderWidth,
        siblingMines: isMine ? -1 : 0,
        coordinates,
        text: '',
        debug: this.debugState,
      };

      result[i].push(tileData);
    }
  }

  for (let y = 0; y < result.length; y++) {
    for (let x = 0; x < result.length; x++) {
      const tile = result[y][x];

      console.assert(typeof tile !== 'undefined', `undefined tile at: x=${x} y=${y}`);

      const coordinates = {
        x,
        y
      };

      if (!tile.isMine) {
        tile.siblingMines = determineSiblingMineCount(result, coordinates, this.numberOfTiles);
        tile.showAlone = tile.siblingMines === 0 && (this.debugState || tile.isOpened);
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
      mineCount: 0,
      openedTileCount: 0,
      msg: messages.default,
      state: states.default,
      debugState: this.debug,
      tiles: generateTiles.call(this)
    };
  },

  methods: {
    getMineCount() {
      let count = 0;

      this.tiles.forEach((row) => {
        row.forEach((tile) => {
          if (tile.isMine) {
            count++;
          }
        });
      });

      return count;
    },
    toggleDebug() {
      this.debugState = !this.debugState;

      this.tiles.forEach((row) => {
        row.forEach((tile) => {
          tile.debug = this.debugState;
        });
      });
    },
    isActive: function () {
      return this.state === states.active;
    },
    reset: function () {
      this.tiles = generateTiles.call(this);
      this.msg = messages.default;
      this.state = states.default;
    },
    openSiblingTiles: function (coordinates) {
      const { xMin, yMin } = getMinCoordinates(coordinates);
      const { xMax, yMax } = getMaxCoordinates(coordinates, this.numberOfTiles);

      for (let y = yMin; y <= yMax; y++) {
        for (let currentX = xMin; currentX <= xMax; currentX++) {
          this.open(this.tiles[y][currentX]);
        }
      }
    },
    afterTileOpen: function (tile) {
      if (tile.isMine) {
        this.state = states.inactive;
        this.msg = messages.mined;

        return;
      }

      this.openedTileCount++;

      if (tile.siblingMines === 0) {
        this.openSiblingTiles(tile.coordinates);

        return;
      }

      if (this.mineCount === 0) {
        this.mineCount = this.getMineCount();
      }

      const allSafeTilesOpen = this.openedTileCount === (this.tiles.length * this.tiles.length) - this.mineCount;

      if (allSafeTilesOpen) {
        this.state = states.inactive;
        this.msg = messages.win;
      }
    },
    open: function (tile) {
      if (tile.isOpened || tile.isMarked || this.state === states.inactive) {
        return;
      }

      tile.text = tile.isMine ? '' : tile.siblingMines;
      tile.isOpened = true;
      this.afterTileOpen(tile);
    },
    mark: function (tile) {
      if (tile.isOpened || this.state === states.inactive) {
        return;
      }

      tile.isMarked = !tile.isMarked;
    },
  },

  computed: {
    cssProps() {
      return {
        '--size': (this.numberOfTiles * 2) + (this.numberOfTiles * 2 * tileBorderWidth) + 'em',
        '--borderWidth': `${tileBorderWidth}em`,
      };
    }
  }
};
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
