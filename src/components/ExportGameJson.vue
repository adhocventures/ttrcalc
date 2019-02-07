<template>
  <div class="export-game-json">
    <b-button size="sm" class="" variant="success" @click="exportCurrentGame();"
      >Export JSON file</b-button
    >
  </div>
</template>

<script>
export default {
  name: "ExportGameJson",
  props: {
    players: Array,
    routeScoring: Array
  },
  methods: {
    exportCurrentGame: function() {
      var exportArr = {
        id: "test",
        timestamp: Date.now(),
        players: this.players,
        routeScoring: this.routeScoring
      };
      console.log(exportArr);
      this.exportJson(exportArr);
    },
    exportJson(arrData) {
      let jsonContent = "data:text/json;charset=utf-8,";
      jsonContent += JSON.stringify(arrData);

      const data = encodeURI(jsonContent);
      const link = document.createElement("a");
      link.setAttribute("href", data);
      link.setAttribute("download", "export.json");
      link.click();
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
