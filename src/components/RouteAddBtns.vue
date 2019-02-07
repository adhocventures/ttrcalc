<template>
  <div class="route-add-btns-wrapper">
    <b-button-group vertical>
      <b-button>Top</b-button>
      <b-button>Middle</b-button>
      <b-button>Bottom</b-button>
    </b-button-group>
  </div>
</template>

<script>
export default {
  name: "RouteAddBtns",
  data() {
    return {
      msg: "Welcome to Your Vue.js App"
    };
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.route-add-btns-wrapper {
  display: block;
  float: right;
}
</style>
