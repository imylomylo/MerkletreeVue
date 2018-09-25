<template>
  <div id="app">
       <HelloWorld/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Montserrat');
#app {
  font-family: 'Montserrat', sans-serif; 
  text-align: center;
  color: #2C3539;
  margin-top: 60px;
}
</style>
