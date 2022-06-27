<template>
  <div
    :style="cssProps"
    :class="{
      tile: true,
      'is-mine': showMine,
      'is-open': tileData.isOpened,
      'no-mines': isAlone,
      'is-marked': tileData.isMarked
    }" >
    {{ tileData.text }}
  </div>
</template>

<script>
export default {
  name: 'TileComponent',

  props: {
    tileData: Object,
  },

  data() {
    return {
      cssProps: (() => {
        return `--borderWidth: ${this.tileData.borderWidth}em;`;
      })()
    };
  },

  computed: {
    showMine() {
      return this.tileData.isMine && (this.tileData.debug || this.tileData.isOpened);
    },
    isAlone() {
      return this.tileData.siblingMines === 0 && (this.tileData.debug || this.tileData.isOpened);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.tile {
  height: 2em;
  width: 2em;
  flex-shrink: 0;
  background-color: var(--closedBg);
  display: flex;
  align-items: center;
  justify-content: center;
  border: var(--borderWidth) solid black;
  color: var(--tileTextColor);
  cursor: pointer;
  user-select: none;
}

.is-open {
  background-color: var(--openedBg);
}

.no-mines {
  background-color: var(--noMinesBg);
}

.is-mine {
  background-color: var(--mineBg);
}

.is-marked {
  background-color: var(--markedBg);
}
</style>
