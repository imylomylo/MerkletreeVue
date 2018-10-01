<template>
  <div id="app">
    <Merkletree/>
    <!--<HelloWorld/>-->
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'
import Merkletree from './components/Merkletree'

export default {
  name: 'App',
  components: {
    HelloWorld,
    Merkletree
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
