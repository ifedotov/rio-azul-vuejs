<template>
  <div class="container" id="app">
    <h2 style="border-bottom: 1px solid grey">Index</h2>
    <ul class="list-group">
      <template v-for="zone in zones(items)">
        <li v-bind:key="zone" class="list-group-item active">
          {{ zone.length ? zone : 'Other' }}
        </li>

        <template v-for="item in buildings(items, zone)">
          <li
            v-bind:key="item.id"
            class="list-group-item"
            v-if="item.black == 0 && item.buildingname.length"
          >
            <a href="https://applefacilities.review.blueriver.com">{{
              item.buildingname
            }}</a>
          </li>
          <li
            v-bind:key="item.id"
            class="list-group-item"
            v-else-if="item.buildingname.length"
          >
            {{ item.buildingname }}
          </li>
        </template>
      </template>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',

  data: function() {
    return {
      items: [],
    };
  },
  methods: {
    zones: function(items) {
      return [...new Set(items.map((x) => x.buildingzone))];
    },
    buildings: function(items, zone) {
      return items.filter((item) => item.buildingzone == zone);
    },
  },
  created() {
    axios
      .get(
        'https://applefacilities.review.blueriver.com/index.cfm/_api/json/v1/scv/building/?andOpenGrouping&locationCode%5B0%5D=sqo&or&locationCode%5B2%5D=nwr&or&locationCode%5B4%5D=scv&or&locationCode%5B6%5D=sfo&closeGrouping&fields=buildingname,buildingabbr,lat,lng,black,buildingZone&active=1&cachedwithin=600'
      )
      .then((response) => {
        this.items = response.data.data.items.sort((a, b) => {
          const aZone = a.buildingzone.length ? a.buildingzone : 'zzzzz';
          const bZone = b.buildingzone.length ? b.buildingzone : 'zzzzz';
          if (aZone + a.buildingname < bZone + b.buildingname) return -1;
          if (aZone + a.buildingname > bZone + b.buildingname) return 1;
          return 0;
        });
      })
      .catch(function(error) {
        console.log(error);
      });
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
