<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>

<style>
#app {
  height: 100%;
  width: 100%;
}
html, body {
  height: 100%;
  width: 100%;
}
* {
  margin: 0;
  padding: 0;
}
</style>
