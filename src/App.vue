<template>
  <div id="app">
    <BlackjackGame />
  </div>
</template>

<script>
import BlackjackGame from './components/BlackjackGame.vue'

export default {
  name: 'App',
  components: { BlackjackGame }
}
</script>

<style>
body {
  background: #f4f4f4;
  font-family: sans-serif;
}
</style>