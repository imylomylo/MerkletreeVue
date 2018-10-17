<template>
  <div id="app">
    <Merkletree/>
  </div>
</template>

<script>
import Merkletree from './components/Merkletree'

export default {
  name: 'App',
  components: {
    Merkletree,
  }
}
</script>

<style>
#app ul {
  list-style: none;
  horizontal-align: center;
  -webkit-padding-start: 0px;
}
#app li {
  display: inline;
  horizontal-align: center;
}
</style>
