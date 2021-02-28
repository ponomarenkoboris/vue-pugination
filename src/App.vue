<template>
  <div class="container">
    <h1>Pugination Practice</h1>
    <Pugination />
  </div>
</template>

<script>
import Pugination from '@/components/Pugination.vue';

export default {
  name: 'Main',
  components: {
    Pugination
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
