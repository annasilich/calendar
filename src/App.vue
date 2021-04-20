<template>
  <div>
    <Calendar />
  </div>
</template>

<script>
import Calendar from "./components/Calendar.vue";
export default {
  components: {
    Calendar,
  },
};
</script>

<style lang="less">
#app {
  font-family: "Roboto Condensed", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
