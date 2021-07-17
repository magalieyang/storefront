<template>
  <div id="app">
    <Showcase  />
  </div>
</template>

<script>
import Showcase from "@/components/showcase";

export default {
  name: 'App',
  components: {
    Showcase,
  },
}
</script>

<style>
@import './assets/css/home.css';
</style>