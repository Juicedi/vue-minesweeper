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

      <span class="icon icon--mine" v-if="showMine">
        <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:krita="http://krita.org/namespaces/svg/krita" xmlns:sodipodi="http://sodipodi.sourceforge.net/DTD/sodipodi-0.dtd" viewBox="0 0 107.28 107.28">
          <circle id="shape0" transform="matrix(1.00000001137866 0 0 1.00000001137866 12.9600001474675 12.9600001474675)" r="41.04" cx="41.04" cy="41.04" fill="#333333" fill-rule="evenodd" stroke="#000000" stroke-opacity="0" stroke-width="0" stroke-linecap="square" stroke-linejoin="bevel"/>
          <rect id="shape1" transform="matrix(1.00000001137866 0 0 1.00000001137866 46.4400005284252 1.44000001638528)" fill="#333333" fill-rule="evenodd" stroke="#000000" stroke-opacity="0" stroke-width="0" stroke-linecap="square" stroke-linejoin="bevel" width="15.12" height="105.12"/>
          <rect id="shape01" transform="matrix(-5.27355942697555e-16 1.00000001598024 -1.00000001598024 -5.27355942697555e-16 106.560001454369 46.4400004936374)" fill="#333333" fill-rule="evenodd" stroke="#000000" stroke-opacity="0" stroke-width="0" stroke-linecap="square" stroke-linejoin="bevel" width="15.12" height="105.12"/>
          <rect id="shape01" transform="matrix(-0.707106797708744 0.707106797708743 -0.707106797708743 -0.707106797708744 96.5112612926976 85.8198065113415)" fill="#333333" fill-rule="evenodd" stroke="#000000" stroke-opacity="0" stroke-width="0" stroke-linecap="square" stroke-linejoin="bevel" width="15.12" height="105.12"/>
          <rect id="shape02" transform="matrix(-0.707106775207191 -0.707106775207193 0.707106775207193 -0.707106775207191 22.1801957301241 96.5112599399042)" fill="#333333" fill-rule="evenodd" stroke="#000000" stroke-opacity="0" stroke-width="0" stroke-linecap="square" stroke-linejoin="bevel" width="15.12" height="105.12"/>
        </svg>
      </span>

      <span class="icon icon--flag" v-if="tileData.isMarked">
        <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:krita="http://krita.org/namespaces/svg/krita" xmlns:sodipodi="http://sodipodi.sourceforge.net/DTD/sodipodi-0.dtd" viewBox="0 0 75.6 123.84">
          <path id="shape0" transform="matrix(1.00000002018358 0 0 1.00000002018358 24.9816238517432 12.2573773317863)" fill="#a81f3d" fill-rule="evenodd" stroke="#000000" stroke-opacity="0" stroke-width="0" stroke-linecap="square" stroke-linejoin="bevel" d="M0 0L47.0184 19.5635L0 39.1271L0 0"/>
          <rect id="shape1" transform="matrix(1.00000002018358 0 0 1.00000002018358 15.6720781414004 4.77866308717731)" fill="#262626" fill-rule="evenodd" stroke="#000000" stroke-opacity="0" stroke-width="0" stroke-linecap="square" stroke-linejoin="bevel" width="12.24" height="111.6"/>
          <rect id="shape2" transform="matrix(1.00000002018358 0 0 1.00000002018358 4.87207792341767 109.898665208876)" fill="#262626" fill-rule="evenodd" stroke="#000000" stroke-opacity="0" stroke-width="0" stroke-linecap="square" stroke-linejoin="bevel" width="33.84" height="9.36000000000001"/>
        </svg>
      </span>
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
      return this.tileData.siblingMines === 0 && this.tileData.debug;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.tile {
  position: relative;
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

.icon {
  position: absolute;
  transform: translate(-50%, -50%);
  top: 50%;
  left: 50%;
  height: 100%;
  width: 100%;
}

.icon svg {
  --size: 70%;
  position: absolute;
  transform: translate(-50%, -50%);
  top: 50%;
  left: 50%;
  height: var(--size);
  width: var(--size);
}
</style>
