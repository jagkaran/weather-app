<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input
        v-model="search"
        @keyup.enter="getWeatherBySearch"
        placeholder="Search"
        dark
        borderless
      >
        <template v-slot:before>
          <q-icon @click="getLocation" name="place" />
        </template>

        <template v-slot:append>
          <q-btn round dense flat icon="search" @click="getWeatherBySearch" />
        </template>
      </q-input>
    </div>
    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">{{ weatherData.name }}</div>
        <div class="text-h6 text-weight light">
          {{ weatherData.weather[0].main }}
        </div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{ Math.round(weatherData.main.temp) }}</span>
          <span class="text-h4 relative-position degree">&deg;C</span>
        </div>
      </div>
      <div class="col text-center">
        <img
          :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
        />
      </div>
    </template>
    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">Weather APP</div>
      </div>
    </template>
    <div class="col skyline"></div>
  </q-page>
</template>

<script>
import axios from "axios";
export default {
  name: "PageIndex",
  data() {
    return {
      search: "",
      weatherData: null,
      lat: null,
      lon: null,
      apiUrl: "https://api.openweathermap.org/data/2.5/weather",
      appKey: "7c489cb39ac22d5c49012e24d575b880",
    };
  },
  computed: {
    bgClass() {
      if (this.weatherData) {
        if (this.weatherData.weather[0].icon.endsWith("n")) {
          return "bg-night";
        } else {
          return "bg-day";
        }
      }
    },
  },
  methods: {
    getLocation() {
      this.$q.loading.show();
      console.log("getlocation clicked");
      navigator.geolocation.getCurrentPosition((position) => {
        console.log("position", position);
        this.lat = position.coords.latitude;
        this.lon = position.coords.longitude;
        this.getWeatherByCoords();
      });
    },
    getWeatherByCoords() {
      this.$q.loading.show();
      axios
        .get(
          `${this.apiUrl}?lat=${this.lat}&lon=${this.lon}&appid=${this.appKey}&units=metric`
        )
        .then((response) => {
          console.log("your location data", response);
          this.weatherData = response.data;
          this.$q.loading.hide();
        });
    },
    getWeatherBySearch() {
      this.$q.loading.show();
      axios
        .get(
          `${this.apiUrl}?q=${this.search}&appid=${this.appKey}&units=metric`
        )
        .then((response) => {
          console.log("your search data", response);
          this.weatherData = response.data;
          this.$q.loading.hide();
        });
    },
  },
};
</script>

<style lang="sass">
.q-page
  background: linear-gradient(to top, #f1f2b5, #135058)
  &.bg-day
    background: linear-gradient(to bottom, #00c9ff, #92fe9d)
  &.bg-night
    background: linear-gradient(to bottom, #000000, #434343)
  .degree
    top: -44px
  .skyline
    flex: 0 0 200px
    background: url(../assets/paris-skyline.png)
    background-size: contain
    background-position: center bottom
</style>
