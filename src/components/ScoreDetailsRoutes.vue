<template>
  <div class="score-details-routes-wrapper">
    <div class="route-table-heading pt-1">
      <h5 class="font-weight-bold">{{ msg }}</h5>
    </div>
    <b-container class="m-0 p-0">
      <b-row>
        <b-col>
          <div class="route-btn-group">
            <b-button
              v-for="(value, index) in btnConfig"
              :key="index"
              @click.stop="addRoute(value);"
              variant="info"
              size="sm"
              class="px-3 route-add-button"
            >
              {{ value.trains }}
            </b-button>
          </div>
          <b-table
            id="detailedRoutes"
            small
            bordered
            :items="routes"
            :fields="fields"
            foot-clone
          >
            <template slot="delete" slot-scope="data">
              <b-button
                size="sm"
                class="py-0"
                variant="danger"
                @click="removeRoute(data);"
                >x</b-button
              >
            </template>
            <template slot="FOOT_trains" slot-scope="data">
              <!-- A custom formatted footer cell  for field 'name' -->
              {{ sumTrains }} Trains
            </template>
            <template slot="FOOT_points" slot-scope="data">
              <!-- A custom formatted footer cell  for field 'name' -->
              {{ sumPoints }} Points
            </template>
            <template slot="FOOT_delete" slot-scope="data"> </template>
          </b-table>
        </b-col>
      </b-row>
      <b-row>
        <b-col>
          <b-button-group v-if="false">
            <b-button
              v-for="(value, index) in btnConfig"
              :key="index"
              @click.stop="addRoute(value);"
              variant="info"
              class="px-3 route-add-button"
            >
              {{ value.trains }}
            </b-button>
          </b-button-group>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
export default {
  name: "ScoreDetailsRoutes",
  props: {
    routes: Array,
    btnConfig: Array,
    sumTrains: Number,
    sumPoints: Number
  },
  data() {
    return {
      msg: "Routes",
      fields: ["trains", "points", "delete"]
    };
  },
  methods: {
    addRoute: function(value) {
      this.routes.push(value);
    },
    removeRoute: function(value) {
      this.routes.splice(value.index, 1);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.route-add-button {
  border-color: #ffffff;
}
.score-details-routes-wrapper {
  display: block;
}
.route-table-heading {
  border-style: solid solid none solid;
  border-radius: 5px 5px 0px 0px;
  border-width: 1px;
}
.route-btn-group {
  display: block;
}
</style>
