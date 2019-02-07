<template>
  <div class="score-details-wrapper">
    <b-card
      :header="'Editing <b>' + name + '</b>'"
      header-bg-variant="primary"
      header-text-variant="white"
      border-variant="primary"
    >
      <b-container fluid class="m-0 p-0">
        <b-row>
          <b-col sm="12" md="6" lg="4" class="mb-3">
            <b-input-group prepend="Name" :size="formInputSize">
              <b-form-input
                v-bind:value.sync="name"
                type="text"
                placeholder="Player Name"
                v-on:input="onInput($event, 'name');"
              ></b-form-input>
            </b-input-group>
            <b-input-group prepend="Longest Route?" :size="formInputSize">
              <b-form-checkbox
                id="input-longest-route"
                v-bind:checked.sync="longestRoute"
                v-on:input="onInput($event, 'longestRoute');"
              >
                <span v-if="longestRoute">Achieved the longest route!</span>
                <span v-else> Not the longest route.</span>
              </b-form-checkbox>
            </b-input-group>
            <b-input-group
              v-if="scoreStations"
              prepend="Unused Stations?"
              :size="formInputSize"
            >
              <b-form-input
                id="input-stations"
                v-bind:value.sync="unusedStations"
                type="number"
                placeholder="Enter the stations NOT played"
                min="0"
                max="3"
                v-on:input="onNumInput($event, 'unusedStations');"
              ></b-form-input>
            </b-input-group>
          </b-col>
          <b-col sm="12" md="6" lg="4" class="mb-3">
            <div v-if="false" class="input-group-text">Destination Cards:</div>
            <div class="destination-label-heading pt-1">
              Destination Cards Points:
            </div>
            <b-input-group prepend="Added:" :size="formInputSize">
              <b-form-input
                id="input-stations"
                v-bind:value.sync="destinationAdds"
                type="number"
                placeholder="Enter Points"
                min="0"
                v-on:input="onNumInput($event, 'destinationAdds');"
              ></b-form-input>
            </b-input-group>
            <b-input-group prepend="Subtracted:" :size="formInputSize">
              <b-form-input
                id="input-stations"
                v-bind:value.sync="destinationSubs"
                type="number"
                placeholder="Enter Points"
                min="0"
                v-on:input="onNumInput($event, 'destinationSubs');"
              ></b-form-input>
            </b-input-group>
          </b-col>
          <b-col sm="auto" md="auto" lg="auto" class="mb-1">
            <ScoreDetailsRoutes
              :routes="routes"
              :btnConfig="btnConfig"
              :sumTrains="totals.trains"
              :sumPoints="totals.points"
            />
          </b-col>
        </b-row>
        <b-row> </b-row>
        <b-row> </b-row>
      </b-container>
    </b-card>
  </div>
</template>

<script>
import ScoreDetailsRoutes from "./ScoreDetailsRoutes";

export default {
  name: "ScoreDetails",
  props: {
    //detail: Object,
    name: String,
    routes: {
      type: Array,
      // Object or array defaults must be returned from
      // a factory function
      default: function() {
        return [];
      }
    },
    longestRoute: Boolean,
    unusedStations: Number,
    destinationAdds: Number,
    destinationSubs: Number,
    score: Object,
    btnConfig: Array,
    scoreStations: {
      type: Boolean,
      default: true
    }
  },
  data() {
    //var temp = {};
    //const entries = Object.entries(this.detail);
    //for (let entry in entries){
    //  temp[entry[0]] = entry[1];
    //}
    return {
      msg: "Route Details",
      formInputSize: "sm"
      //name: this.detail.name,
      //routes: this.detail.routes,
      //longestRoute: this.detail.longestRoute,
      //stations: this.detail.stations,
      //destinationAdds: this.detail.destinationAdds,
      //destinationSubs: this.detail.destinationSubs
    };
  },
  computed: {
    totals: function() {
      //console.log(this);
      //let entries = Object.values(this.routes);
      //console.log(entries);
      var trains = 0;
      var points = 0;
      this.routes.forEach(function(element) {
        //console.log(element);
        trains += element.trains;
        points += element.points;
      });
      //for (let entry in entries) {
      //   console.log(entry);
      //   trains = trains + entry.trains;
      //  points = points + entry.points;
      //}
      return { trains: trains, points: points };
    }
  },
  watch: {
    routes: function(val, oldVal) {
      this.$nextTick(function() {
        this.updateScore("test");
      });
    },
    scoreStations: function(val, oldVal) {
      console.log(val);
      console.log(oldVal);
      this.$nextTick(function() {
        this.updateScore("test");
      });
    }
    // c: {
    //    handler: function (val, oldVal) { /* ... */ },
    //    deep: true
    //  },
  },
  methods: {
    onInput: function(event, prop) {
      //console.log(event);
      //console.log(this); //destinationAdds
      this.$emit("update:" + prop, event);
      this.$nextTick(function() {
        this.updateScore("test");
      });
    },
    onNumInput: function(event, prop) {
      //console.log(event);
      //console.log(this); //destinationAdds
      this.$emit("update:" + prop, parseInt(event));
      this.$nextTick(function() {
        this.updateScore("test");
      });
    },
    updateScore: function(updated) {
      //console.log(this);
      var score = this.score;
      var routePoints = 0;
      this.routes.forEach(function(element) {
        //console.log(element);
        routePoints += element.points;
      });
      score.routes = routePoints;
      if (this.scoreStations) {
        score.stations = this.unusedStations * 4;
      } else {
        score.stations = 0;
      }

      if (this.longestRoute) {
        score.longestRoute = 10;
      } else {
        score.longestRoute = 0;
      }

      score.total =
        score.routes +
        score.stations +
        score.longestRoute +
        this.destinationAdds -
        this.destinationSubs;
      //this.$emit("update:score", score);
    }
  },
  components: {
    ScoreDetailsRoutes
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.destination-label-heading {
  border-style: solid solid none solid;
  border-radius: 5px 5px 0px 0px;
  border-width: 1px;
  /* background-color: #e9ecef; */
}
</style>
