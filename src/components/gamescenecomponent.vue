<template>
  <div id="game-scene" class="scene">
    <div id="floor" class="floor">
      <div
        v-for="tile in tiles"
        :key = "tile.id"
      >
        <div :class="tile.tileClass" :style="tile.tileStyle">
        </div>
      </div>
      <div id="player" :class="isOnLane1 ? 'lane1' : 'lane2'">
      </div>
    </div>
  </div>
</template>
<script>
import Constant from './constant.vue'
export default {
  data () {
    return {
      tiles: [],
      id: 0,
      runway: [
        [0, 1, 2, 0],
        [0, 1, 2, 0],
        [0, 1, 2, 0],
        [0, 1, 2, 4],
        [0, 1, 2, 0],
        [0, 100, 2, 0],
        [0, 1, 2, 0],
        [0, 1, 2, 0],
        [0, 1, 2, 0],
        [0, 1, 2, 0],
        [0, 1, 2, 0],
        [0, 1, 2, 0],
        [4, 1, 100, 0],
        [0, 1, 2, 0],
        [0, 1, 2, 0]
      ],
      round: 0,
      runwayIndex: 0,
      isGameOver: false,
      BOUNDARY: 1000,
      TILE_WIDTH: 100,
      TILE_HEIGHT: 100,
      isOnLane1: true,
      isBlock: false
    }
  },
  components: {},
  created () {
  },
  mounted () {
    Constant.Event.$on(Constant.MSG_SHOW_GAME_SCENE, function () {
      this.init()
    }.bind(this))
  },
  computed: {},
  methods: {
    createTile (type, x, y) {
      let tile = {}
      tile.tileClass = 'tile tile-' + type
      tile.x = x
      tile.y = y
      tile.id = this.id++
      tile.tileStyle = 'transform: translate3d(' + x + 'px, ' + y + 'px, 0)'
      if (type === 100) {
        tile.isBlock = true
      }
      this.tiles.push(tile)
    },
    resetTiles () {
      this.tiles = []
    },
    init () {
      //  开始执行绘制
      this.startOver()
      window.onkeyup = this.handleKeyUp
    },
    handleKeyUp (e) {
      if (e.keyCode === 37) {
        this.isOnLane1 = true
      } else if (e.keyCode === 39) {
        this.isOnLane1 = false
      }
      return false
    },
    startOver () {
      //  重置数据
      this.resetTiles()
      this.round = 0
      this.runwayIndex = 0
      this.isGameOver = false
      setTimeout(this.tick, 800)
      //  调用浏览器接口requestAnimationFrame，传入一个函数，然后将函数在内部调用，该函数可以依照浏览器显示频率进行绘制而不会因为频率高于浏览器导致动画掉帧等问题以及资源高效利用,能通过CPU负载进行循环强度
      requestAnimationFrame(this.onFrame)
    },
    onFrame () {
      this.updateTilesPosition()
      requestAnimationFrame((this.onFrame))
    },
    // 以上逻辑等于是while(1){ this.updateTilesPosition() }
    updateTilesPosition () {
      // 循环一：依照当前数据不断的进行绘制
      for (let i = 0; i < this.tiles.length; i++) {
        this.tiles[i].tileStyle = 'transform:translate3d(' + this.tiles[i].x + 'px, ' + this.tiles[i].y + 'px, 0)'
      }
    },
    //  循环二 更改页面贴图的Y坐标
    tick () {
      this.round += 2
      this.runwayTick(this.round)
      //  加快绘制频率
      if (!this.isGameOver) {
        let duration = Math.max(801 - this.round, 100)
        setTimeout(this.tick, duration)
      }
    },
    //  进行新增绘制
    runwayTick (round) {
      this.moveTilesDown()
      this.runwayIndex += 1
      if (this.runwayIndex >= this.runway.length) {
        this.runwayIndex = 0
      }
      let row = this.runway[this.runwayIndex]
      for (let i = 0, len = row.length; i < len; i++) {
        this.createTile(row[i], i * this.TILE_WIDTH, 0)
      }
    },
    //  将当前绘制的Y坐标进行下移
    moveTilesDown () {
      for (let i = 0; i < this.tiles.length; i++) {
        this.tiles[i].y += this.TILE_HEIGHT
        if (this.tiles[i].y > this.BOUNDARY) {
          this.tiles.splice(i, 1)
        } else {
          this.checkCollision(this.tiles[i])
        }
      }
    },
    checkCollision (tile) {
      if (tile.isBlock) {
        if (tile.y === 400) {
          if ((tile.x === this.TILE_WIDTH && this.isOnLane1 === true) || (tile.x === this.TILE_WIDTH * 2 && this.isOnLane1 === false)) {
            this.isGameOver = true
            Constant.Event.$emit(Constant.MSG_SHOW_GAMEOVER_SCENE)
          }
        }
      }
    }
  }
}
</script>
<style lang="stylus" scoped>
  .floor {
    position relative
    width 400px
    height 100%
    margin auto
    background url(../../static/images/space-runner-bg.png) 0 0;
  }
  .tile {
    position: absolute;
    width: 100px;
    height: 100px;
  }
  .tile-1 {
    background: url(../../static/images/runway.png);
  }
  .tile-2 {
    background: url(../../static/images/runway2.png);
  }
  .tile-100 {
    background: url(../../static/images/block.png);
  }
  .tile-4 {
    background: url(../../static/images/star.png) center center no-repeat;
  }
  #player {
    position absolute
    width 100px
    height 100px
    background-image url("../../static/images/running.png")
    bottom 100px
    animation running 0.4s steps(2) infinite
  }
  @keyframes running {
    from {background-position: 0;}
    to   {background-position: -200px;}
  }
  #player.lane1 {
    transform translate3d(100px,0,0)
  }
  #player.lane2 {
    transform translate3d(200px,0,0)
  }
</style>
