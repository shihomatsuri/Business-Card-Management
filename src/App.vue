<template>
  <div id="app">
    <div class="data-box">
      <business-card-list></business-card-list>
    </div>
  </div>
</template>

<script>
import BusinessCardList from "./components/BusinessCardList.vue";

export default {
  name: "app",
  components: {
    BusinessCardList,
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.data-box
{
  margin: 5px;
  padding: 20px;
  height: 80vh;
  border-radius: 25px;
  border: 5px solid #e8edf7;
  background: #ffffff;
}
</style>
