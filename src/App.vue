<template>
  <div id="app">
    <img src="./assets/logo-bizcap.svg">
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
  margin: auto;
  padding: 70px 50px;
  width: 430px;
}

#app img{display: block; margin: auto}
</style>
