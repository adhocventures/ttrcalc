<template>
  <div class="score-details-wrapper">
    <b-form-input
      v-bind:value.sync="name"
      type="text"
      placeholder="Player Name"
      v-on:input="onInput($event, 'name');"
    ></b-form-input>
    <b-form-input
      :value="destinationAdds"
      type="number"
      placeholder="Enter a number"
      min="0"
      v-on:input="onNumInput($event, 'destinationAdds');"
    ></b-form-input>
  </div>
</template>

<script>
export default {
  name: "ScoreDetailPlayer",
  props: {
    name: String,
    destinationAdds: Number
  },
  methods: {
    onChange: function(event) {
      console.log(event);
      console.log(this);
      this.$emit("update:name", event.target.value);
    },
    onChange2: function(event) {
      console.log(event);
      console.log(this);
      this.$emit("update:destinationAdds", event.target.value);
    },
    onInput: function(event, prop) {
      console.log(event);
      console.log(this); //destinationAdds
      this.$emit("update:" + prop, event);
    },
    onNumInput: function(event, prop) {
      console.log(event);
      console.log(this); //destinationAdds
      this.$emit("update:" + prop, parseInt(event));
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
