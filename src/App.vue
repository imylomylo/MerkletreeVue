<template>
  <div id="app">
    <mTree/>
    <!--<Merkletree/>
    <HelloWorld/>-->
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'
import Merkletree from './components/Merkletree'
import mTree from './components/mTree'

export default {
  name: 'App',
  components: {
    HelloWorld,
    Merkletree,
    mTree
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
