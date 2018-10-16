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
.label {
  font-size: 1vw;
}
.big {
font-size: 1vw;
}

</style>
