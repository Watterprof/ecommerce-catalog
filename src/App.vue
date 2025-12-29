<template>
  <div id="app">
    <ProductDisplay />
  </div>
</template>

<script>
import ProductDisplay from './components/ProductDisplay.vue';


export default {
  name: 'App',
  components: {
    ProductDisplay
  }
};
</script>

<style>
@import './assets/style/page.css';

#app {
  width: 100%;
  min-height: 100vh;
}
</style>