<template>
  <div id='app'>
    <proxy />
  </div>
</template>

<script>
import Proxy from './components/Proxy.vue'
export default {
  name: 'app',
  components: {
    Proxy
  }
};
</script>

<style>
@import  '../node_modules/bootstrap/dist/css/bootstrap.css';
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
html {
  font-size: 14px;
}

@media (max-width: 768px) {
  html {
    font-size: 12px;
  }
}
</style>
