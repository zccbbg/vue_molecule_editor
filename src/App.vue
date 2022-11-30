<template>
  <div id="app">
    <MoleculeIndex/>
  </div>
</template>

<script>
import MoleculeIndex from './components/MoleculeIndex'

export default {
  name: 'App',
  components: {
    MoleculeIndex
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /*text-align: center;*/
  /*color: #2c3e50;*/
  /*margin-top: 60px;*/
}
</style>
