<template>
  <div id="game-scene"  :class="[show ? 'in' : 'out']">
    <p>
      <a href="javascript:;" id="start-btn" class="button" @click = "startGame"></a>
    </p>
  </div>
</template>

<script>
import Constant from './constant.vue'
export default {
  data () {
    return {
      show: true
    }
  },
  mounted () {
    console.log('start scene mounted')
    Constant.Event.$on(Constant.MSG_SHOW_START_SCENE, function () {
      this.show = true
    }.bind(this))
  },
  methods: {
    startGame () {
      this.show = false
      Constant.Event.$emit(Constant.MSG_SHOW_GAME_SCENE)
    }
  }
}
</script>
<style scoped>
.button {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
}
#game-scene {
  background: url("../../static/images/start-scene-bg.png");
}
</style>
