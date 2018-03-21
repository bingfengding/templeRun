<template>
  <div class="container">
    <header>
      <div class="row">
        <h1>Space Runner!</h1>
      </div>
    </header>
    <section id="game" class="row">
      <gamescenecomponent class="scene"></gamescenecomponent>
      <gameoverscene class="scene"></gameoverscene>
      <startscene class="scene"></startscene>
    </section>
  </div>
</template>
<script>
import startscene from './startscenecomponent.vue'
import gameoverscene from './gameoverscenecomponent.vue'
import gamescenecomponent from './gamescenecomponent.vue'
export default {
  components: {
    startscene,
    gameoverscene,
    gamescenecomponent
  }
}
</script>
<style scoped lang="stylus">
.container {
  margin: 0;
  padding: 0;
  color: #2e3b3d;
  background: #a3d7df;
}
p {
  margin: 0;
  padding: 1em 0;
}
#game {
  width: 480px;
  height: 600px;
  margin: 0 auto;
  border-radius: 8px;
  text-align: center;
  position: relative;
  overflow: hidden;
}
.row {
  width: 480px;
  margin: 0 auto;
  text-align center
}
.scene {
  background: #f9f9f9;
  width: 100%;
  height: 100%;
  position: absolute;
  overflow: hidden;
  border-radius: 8px;
  transition: all .3s ease-out;
}
.scene.out {
  transform: translate3d(100%, 0, 0);
}
.scene.in {
  transform: translate3d(0, 0, 0);
}
.hidden {
  display: none;
}
</style>
