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
@import url('https://fonts.googleapis.com/css?family=Merriweather:400i,700i');

#app {
  font-family:'Merriweather', serif;
  text-align: center;
  color: #2C3539;
  margin-top: 60px;
}
</style>
