<template>
  <div id="app">
    <!-- <HelloWorld></HelloWorld> -->
    <Calender></Calender>
  </div>
</template>

<script>
import Calender from './components/calender'
import HelloWorld from './components/HelloWorld'

export default {
  name: 'app',
  components: {
    Calender,
    HelloWorld
  }
}
</script>

<style lang="less">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
