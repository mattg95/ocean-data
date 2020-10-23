<template>
  <div class="app" id="app">
    <Header class="header" />
    <Body class="body" />
  </div>
</template>

<script>
//App only features two vue components, 'header' and 'body'. 'Map' is a child of 'body' and 'sidebar' is a child of map.
import Header from "./Header.vue";
import Body from "./Body.vue";

export default {
  name: "app",
  components: {
    Header,
    Body,
  },
};
</script>

<style lang="scss">
@import "./styles/stylesheet.scss";
</style>
