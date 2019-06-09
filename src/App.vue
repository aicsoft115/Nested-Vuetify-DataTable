<template>
  <div id="app">
    <NestedDataTable msg="" />
  </div>
</template>

<script>
import NestedDataTable from './components/NestedDataTable'

export default {
  name: 'app',
  components: {
    NestedDataTable
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
