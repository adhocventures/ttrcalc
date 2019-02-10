<template>
  <div id="app">
    <ScoreSummary
      :players="players"
      :routeScoring="config.routeScoring"
      :scoreStations="config.scoreStations"
    />
    <div class="config-bar mx-2">
      <ConfigCtl
        :players="players"
        v-bind:routeScoring.sync="config.routeScoring"
        v-bind:retainScore.sync="config.retainScore"
        v-bind:retainHistory.sync="config.retainHistory"
        v-bind:scoreStations.sync="config.scoreStations"
        v-on:saveroutes="saveConfig"
        v-on:loadroutes="loadRoutes"
      />
    </div>
  </div>
</template>

<script>
import ConfigCtl from "./components/ConfigCtl";
import ScoreSummary from "./components/ScoreSummary";

var STORAGE_KEY = "ttr-history";
var historyStorage = {
  fetch: function() {
    var history = JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]");
    history.forEach(function(game, index) {
      game.id = index;
    });
    historyStorage.numGames = history.length;
    return history;
  },
  save: function(history) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(history));
  }
};

var CONFIG_KEY = "ttr-config";
var resultStorage = {
  fetch: function() {
    var config = JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]");

    return config;
  },
  save: function(config) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(config));
  }
};

var tempScores = [
  {
    name: "Player Name",
    routes: [],
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
  },
  
];

export default {
  name: "App",
  //props: {
  //  players: Array,
  //  scoreDetails: Object,
  //  routeScoring: Array
  // },
  data() {
    return {
      players: tempScores,
      config: {
        retainScore: false,
        retainHistory: false,
        scoreStations: true,
        routeScoring: [
          { trains: 1, points: 1 },
          { trains: 2, points: 2 },
          { trains: 3, points: 4 },
          { trains: 4, points: 7 },
          { trains: 6, points: 15 },
          { trains: 8, points: 21 }
        ]
      },
      saveTimer: false
    };
  },
  computed: {
    autoCacheGame: function() {
      //quick hack so we can watch the nested variable
      return this.config.retainScore;
    }
  },
  methods: {
    saveConfig: function(data) {
      console.log(data);
      //save routes
      STORAGE_KEY = "ttr-routes";
      localStorage.setItem(STORAGE_KEY, JSON.stringify(data));
    },
    loadLocalGameCache: function() {
      var STORAGE_KEY = "ttr-gamecache";
      var game = JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]");
      console.log(game);
      this.players.splice(0, this.players.length, ...game);
    },
    saveLocalGameCache: function() {
      var STORAGE_KEY = "ttr-gamecache";
      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.players));
      console.log("game saved");
      //@TODO: add messaging confirming save
    },
    save: function(config) {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(config));
    },
    loadRoutes: function() {
      STORAGE_KEY = "ttr-routes";
      var config = JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]");
      //console.log(config);
      this.config.routeScoring.splice(
        0,
        this.config.routeScoring.length,
        ...config
      );
    }
  },
  watch: {
    players: {
      handler: function(val, oldVal) {
        //using a timing so we are not constantly saving to the local store
        if (this.config.retainScore) {
          clearTimeout(this.saveTimer);
          this.saveTimer = setTimeout(this.saveLocalGameCache, 5 * 1000);
          console.log("presave prepped");
        }
      },
      deep: true
    },
    autoCacheGame: function(val) {
      var STORAGE_KEY = "ttr-autocache";
      localStorage.setItem(STORAGE_KEY, val);
    }
  },
  mounted: function() {
    //check for local storage config and then load.
    var autoload = localStorage.getItem("ttr-autocache");
    console.log(autoload);
    if (autoload) {
      this.loadLocalGameCache();
      this.config.retainScore = true;
    }
  },
  components: {
    ConfigCtl,
    ScoreSummary
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}
.config-bar {
  float: right;
}
</style>
