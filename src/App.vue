<template>
  <div id="app">
    <GameCard />
  </div>
</template>

<script>
import GameCard from './components/GameCard.vue'

export default {
  name: 'App',
  components: {
    GameCard
  }
}
</script>

<style lang="scss">
  body {
    margin: 0;
  }

  #app {
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    /* background-color: #6EB0FF; */
    background-image: url('./assets/world-map.jpg');
    background-position: center;
    background-size: cover;
  }
</style>
