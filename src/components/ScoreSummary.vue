<template>
  <div class="score-details-wrapper">
    <b-card title="Score Calculator">
      <b-table
        responsive
        small
        striped
        outlined
        :items="players"
        :fields="fields"
      >
        <template slot="actions" slot-scope="data">
          <b-button
            @click.stop="data.toggleDetails"
            class="mr-2"
            :variant="data.detailsShowing ? 'warning' : 'primary'"
          >
            {{ data.detailsShowing ? "Editing" : "Edit" }}
          </b-button>
          <b-button variant="danger" v-b-modal="'cd-' + data.index.toString()"
            >X</b-button
          >
          <b-modal
            :id="'cd-' + data.index.toString()"
            title="Confirm Delete?"
            header-bg-variant="primary"
            header-text-variant="light"
            ok-title="Cancel"
            ok-only
          >
            <b-button variant="danger" @click="removePlayer(data);"
              >Confirm Deletion of {{ data.item.name }}</b-button
            >
          </b-modal>
        </template>
        <template slot="row-details" slot-scope="row">
          Click the 'Editing' button above or 'Save and Close' button below to
          stop editing.
          <ScoreDetails
            v-bind.sync="row.item"
            :btnConfig="routeScoring"
            :scoreStations="scoreStations"
          />
          <b-card>
            <b-button variant="warning" @click="row.toggleDetails"
              >Save and Close</b-button
            >
          </b-card>
        </template>
      </b-table>
      <div slot="footer">
        <b-button variant="primary" @click="addPlayer();">Add Player</b-button>
      </div>
    </b-card>
  </div>
</template>

<script>
import ScoreDetails from "./ScoreDetails";
import ScoreDetailPlayer from "./ScoreDetailPlayer";
export default {
  name: "ScoreSummary",
  props: {
    players: Array,
    routeScoring: Array,
    scoreStations: Boolean
  },
  data() {
    var tempData = {
      msg: "Place Holder for Score Summary",
      fields: [
        {
          key: "name",
          sortable: true
        },
        {
          key: "score.routes",
          label: "Routes",
          sortable: true,
          class: ["d-none", "d-sm-table-cell"]
        },
        {
          key: "score.longestRoute",
          label: "Longest Route",
          sortable: true,
          class: ["d-none", "d-sm-table-cell"]
        },
        {
          key: "score.stations",
          label: "Stations",
          sortable: true,
          class: ["d-none", "d-sm-table-cell"]
        },
        {
          key: "destinationAdds",
          label: "+",
          sortable: true,
          class: ["d-none", "d-sm-table-cell"]
          //variant: "success"
        },
        {
          key: "destinationSubs",
          label: "-",
          sortable: true,
          formatter: value => {
            return "-" + value;
          },
          class: ["d-none", "d-sm-table-cell"]
          // Variant applies to the whole column, including the header and footer
          //variant: "danger"
        },
        {
          key: "score.total",
          label: "Total",
          sortable: true
          // Variant applies to the whole column, including the header and footer
          //variant: "primary"
        },
        {
          key: "actions",
          label: ""
        }
      ]
    };

    if (!this.scoreStations) {
      tempData.fields[3].class = "d-none";
    }

    return tempData;
  },
  computed: {},
  methods: {
    removePlayer: function(data) {
      //console.log(data);
      this.$root.$emit("bv::hide::modal", "cd-" + data.index.toString());
      this.players.splice(data.index, 1);
    },
    addPlayer: function(data) {
      this.players.push({
        name: "New Player",
        routes: [
          {
            trains: 1,
            points: 1
          },
          {
            trains: 3,
            points: 4
          }
        ],
        longestRoute: false,
        unusedStations: 3,
        destinationAdds: 0,
        destinationSubs: 0,
        score: {
          routes: 0,
          stations: 12,
          longestRoute: 0,
          destAdd: 0,
          destSub: 0,
          total: 12
        }
      });
    }
  },
  watch: {
    scoreStations: function(val) {
      var tempData = this.fields.filter(function(el) {
        return el.key === "score.stations";
      });
      //console.log(tempData);
      if (val) {
        //show stations
        tempData[0].class = ["d-none", "d-sm-table-cell"];
      } else {
        tempData[0].class = ["d-none"];
      }
    }
  },
  components: { ScoreDetails, ScoreDetailPlayer }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
