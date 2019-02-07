<template>
  <div class="config-ctl-wrapper">
    <b-btn v-b-modal.configCtl>Settings</b-btn>
    <b-modal
      id="configCtl"
      title="Configure Settings"
      header-bg-variant="primary"
      header-text-variant="light"
      ok-title="Close"
      ok-only
    >
      <b-tabs>
        <b-tab title="Scoring" active>
          <b-container>
            <b-row class="mt-3">
              <b-col>
                <b-form-checkbox
                  v-bind:checked.sync="scoreStations"
                  v-on:input="onInput($event, 'scoreStations');"
                >
                  Include <em>Stations</em> for score calculations (e.g. TTR
                  Europe).
                </b-form-checkbox>
              </b-col>
            </b-row>
            <hr />
            <b-row>
              <b-col> <h5>Route Trains to Points Configuration</h5> </b-col>
            </b-row>
            <b-row align-v="end" align-h="center">
              <b-col cols="2"> Order </b-col>
              <b-col> # of Trains </b-col>
              <b-col> Points Earned </b-col>
              <b-col cols="1"> </b-col>
            </b-row>
            <hr />
            <b-form-row
              align-v="end"
              align-h="center"
              v-for="(value, index) in routeScoring"
              :key="index"
            >
              <b-col cols="2">{{ index }}</b-col>
              <b-col>
                <b-form-input
                  v-model.number="value.trains"
                  type="number"
                  placeholder="value"
                ></b-form-input>
              </b-col>
              <b-col>
                <b-form-input
                  v-model.number="value.points"
                  type="number"
                  placeholder="Enter a number"
                  min="0"
                ></b-form-input>
              </b-col>
              <b-col cols="1">
                <b-button
                  size="sm"
                  variant="danger"
                  @click="removeRoute(index);"
                  >X
                </b-button>
              </b-col>
            </b-form-row>
            <b-row align-h="end">
              <b-col cols="8">
                <b-button size="sm" variant="secondary" @click="addRoute();"
                  >Add Row
                </b-button>
              </b-col>
              <b-col cols="2">
                <b-button
                  size="sm"
                  variant="secondary"
                  @click="resetRouteScoring();"
                  >Reset
                </b-button>
              </b-col>
            </b-row>
            <hr />
          </b-container>
        </b-tab>
        <b-tab title="Storage">
          <b-container fluid>
            <b-row class="mt-3">
              <b-col>
                <b-form-checkbox
                  v-bind:checked.sync="retainScore"
                  v-on:input="onInput($event, 'retainScore');"
                >
                  Automatically Cache Current Game Locally (to browser)
                </b-form-checkbox>
                <div class="font-italic">
                  NOTE: If this option is not checked, all data entered will be
                  lost if you navigate away from the page (or accidently hit the
                  back button in your browser).
                </div>
                <hr />
              </b-col>
            </b-row>
            <b-row class="mt-3">
              <b-col>
                <b-form-checkbox
                  disabled
                  indeterminate
                  v-bind:checked.sync="retainHistory"
                  v-on:input="onInput($event, 'retainHistory');"
                >
                  Store Game History (Under Development)
                </b-form-checkbox>
                <hr />
              </b-col>
            </b-row>
            <b-row>
              <b-col>
                <b-button
                  size="sm"
                  variant="secondary"
                  @click="$emit('saveroutes', routeScoring);"
                  class="m-1"
                >
                  Save Route Scoring to Local Storage
                </b-button>
              </b-col>
              <b-col>
                <b-button
                  size="sm"
                  variant="secondary"
                  @click="$emit('loadroutes');"
                  class="m-1"
                  >Load Route Scoring from Local Storage
                </b-button>
              </b-col>
            </b-row>
          </b-container>
        </b-tab>
        <b-tab title="Export">
          <b-container>
            <b-row>
              <b-col class="mt-3">
                <b-button
                  size="sm"
                  variant="success"
                  @click="exportCurrentGame();"
                  >Download CSV of Current Game
                </b-button>
              </b-col>
            </b-row>
            <b-row>
              <b-col class="mt-3">
                <ExportGameJson
                  :players="players"
                  :routeScoring="routeScoring"
                />
              </b-col>
            </b-row>
          </b-container>
        </b-tab>
      </b-tabs>
    </b-modal>
  </div>
</template>

<script>
import ExportGameJson from "./ExportGameJson";
export default {
  name: "ConfigCtl",
  props: {
    retainScore: Boolean,
    retainHistory: Boolean,
    scoreStations: Boolean,
    players: Array,
    routeScoring: Array
  },
  methods: {
    onInput: function(event, prop) {
      //console.log(event);
      //console.log(this); //destinationAdds
      this.$emit("update:" + prop, event);
    },
    onNumInput: function(event, prop) {
      //console.log(event);
      //console.log(this); //destinationAdds
      this.$emit("update:" + prop, parseInt(event));
    },
    addRoute: function(value) {
      this.routeScoring.push({ trains: 0, points: 0 });
    },
    removeRoute: function(value) {
      //console.log(value);
      this.routeScoring.splice(value, 1);
    },
    resetRouteScoring: function() {
      var routeScoring = [
        { trains: 1, points: 1 },
        { trains: 2, points: 2 },
        { trains: 3, points: 4 },
        { trains: 4, points: 7 },
        { trains: 5, points: 10 },
        { trains: 6, points: 15 },
        { trains: 8, points: 21 }
      ];
      this.routeScoring.splice(0, this.routeScoring.length, ...routeScoring);
      return;
    },
    exportCurrentGame: function() {
      var exportArr = [];
      this.players.forEach(function(el) {
        console.log(el);
        var tempObj = Object.assign({ name: el.name }, el.score);
        //tempObj.destAdd = el.destinationAdds;
        //tempObj.destSub = el.destinationSubs;
        //tempObj["name"] = el.name;
        exportArr.push(tempObj);
      });
      console.log(exportArr);
      this.csvExport(exportArr);
    },
    csvExport(arrData) {
      let csvContent = "data:text/csv;charset=utf-8,";
      csvContent += [
        Object.keys(arrData[0]).join(";"),
        ...arrData.map(item => Object.values(item).join(";"))
      ]
        .join("\n")
        .replace(/(^\[)|(\]$)/gm, "");

      const data = encodeURI(csvContent);
      const link = document.createElement("a");
      link.setAttribute("href", data);
      link.setAttribute("download", "export.csv");
      link.click();
    }
  },
  components: {
    ExportGameJson
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
