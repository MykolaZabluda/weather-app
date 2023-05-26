<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <CardBlock/>
  </div>
</template>

<script>
import CardBlock from './components/CardBlock.vue';

export default {
  name: 'App',
  components: {
    CardBlock
  }
}
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  // background: #80c8bc;
}

img {
  width: 100%;
  max-width: 130px;
  height: auto;
  margin-top: 15px;
}
</style>
