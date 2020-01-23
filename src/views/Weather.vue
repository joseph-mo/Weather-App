<template>
  <div class="weather">
    <router-link to="/">←</router-link>
    <!-- Image Sources: 
    Austin: https://images5.alphacoders.com/310/310426.jpg 
    Boston: https://images.unsplash.com/photo-1540888513280-fac1aa56c69f?ixlib=
    rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&w=1000&q=80
    New York: https://previews.123rf.com/images/peshkov/peshkov1904/peshkov1904
    01017/121172277-creative-new-york-city-background-skyline-tourism-and-downtown-concept.jpg
    San Fran: https://residency-ncal.kaiserpermanente.org/wp-content/uploads/2019/02/hero-sf.jpg-->
    <img class="background" v-bind:src="imageSource()" alt="background" />
    <h1>{{ this.city }}</h1>
    <div class="weatherPanels">
      <WeatherPanel
        :day="today"
        :weatherState="todaysWeatherState"
        :low="todaysLowTemp"
        :high="todaysHighTemp"
        :windSpeed="todaysWindSpeed"
        :humidity="todaysHumidity"
        :loading="loading"
        :first="first"
        :icon="todaysIconURL"
      />
      <WeatherPanel
        :day="tomorrow"
        :weatherState="tomorrowsWeatherState"
        :low="tomorrowsLowTemp"
        :high="tomorrowsHighTemp"
        :windSpeed="tomorrowsWindSpeed"
        :humidity="tomorrowsHumidity"
        :loading="loading"
        :icon="tomorrowsIconURL"
      />
      <WeatherPanel
        :day="inTwoDays"
        :weatherState="inTwoDaysWeatherState"
        :low="inTwoDaysLowTemp"
        :high="inTwoDaysHighTemp"
        :windSpeed="inTwoDaysWindSpeed"
        :humidity="inTwoDaysHumidity"
        :loading="loading"
        :icon="inTwoDaysIconURL"
      />
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import WeatherPanel from "@/components/WeatherPanel.vue";
import moment from "moment";

export default {
  name: "weather",
  components: {
    WeatherPanel
  },
  data: function() {
    return {
      today: null,
      tomorrow: null,
      inTwoDays: null,
      todaysWeatherState: null,
      todaysLowTemp: null,
      todaysHighTemp: null,
      tomorrowsWeatherState: null,
      tomorrowsLowTemp: null,
      tomorrowsHighTemp: null,
      inTwoDaysWeatherState: null,
      inTwoDaysLowTemp: null,
      inTwoDaysHighTemp: null,
      todaysWindSpeed: null,
      tomorrowsWindSpeed: null,
      inTwoDaysWindSpeed: null,
      todaysHumidity: null,
      tomorrowsHumidity: null,
      inTwoDaysHumidity: null,
      city: null,
      loading: false,
      first: true,
      todaysIconURL: null,
      tomorrowsIconURL: null,
      inTwoDaysIconURL: null,
      currCity: ""
    };
  },
  methods: {
    // Inspiration taken from online research
    imageSource: function() {
      if (this.currCity != "") {
        var images = require.context("../assets/", false, /\.jpg$/);
        return images("./" + this.currCity + ".jpg");
      }
    }
  },
  mounted() {
    // Finds current day, tomorrow, and in 2 days using moment library
    this.currCity = localStorage.getItem("currCity");
    let currCityidx = this.currCity.indexOf(",");
    this.currCity = this.currCity.substring(currCityidx + 2);

    let currentDay = moment().calendar();
    let currentDaySpaceIdx = currentDay.indexOf(" ");
    currentDay = currentDay.substring(0, currentDaySpaceIdx);
    this.today = currentDay;

    let tomorrow = moment()
      .add(1, "days")
      .calendar();
    let tomorrowSpaceIdx = tomorrow.indexOf(" ");
    tomorrow = tomorrow.substring(0, tomorrowSpaceIdx);
    this.tomorrow = tomorrow;

    let inTwoDays = moment()
      .add(2, "days")
      .calendar();
    let inTwoDaysSpaceIdx = inTwoDays.indexOf(" ");
    inTwoDays = inTwoDays.substring(0, inTwoDaysSpaceIdx);
    this.inTwoDays = inTwoDays;

    // Retrieves respective woeid of city and defines Axios/Cors-Anywhere URLs
    var currentWoeid = this.$route.params["woeid"];
    const axios = require("axios");
    var cors_api_url = "https://cors-anywhere.herokuapp.com/";
    var api_url = "metaweather.com/api/location/" + currentWoeid + "/";

    // Promise resolution passes response to function to we are
    // able to extract data
    axios.get(cors_api_url + api_url).then(response => {
      var myJSON = JSON.stringify(response);
      var myObj = JSON.parse(myJSON);

      // Data retrieved for current city's weather being looked at
      this.city = myObj.data.title;

      /* Icons retrieved for each day's weather state */
      let baseURL = "https://www.metaweather.com/";
      let todayAbbrev =
        myObj.data.consolidated_weather[0]["weather_state_abbr"];
      this.todaysIconURL =
        baseURL + "static/img/weather/" + todayAbbrev + ".svg";
      let tomorrowAbbrev =
        myObj.data.consolidated_weather[1]["weather_state_abbr"];
      this.tomorrowsIconURL =
        baseURL + "static/img/weather/" + tomorrowAbbrev + ".svg";

      let inTwoDaysAbbrev =
        myObj.data.consolidated_weather[2]["weather_state_abbr"];
      this.inTwoDaysIconURL =
        baseURL + "static/img/weather/" + inTwoDaysAbbrev + ".svg";

      // Weather objects for each day (Today, tomorrow, in 2 days) */
      var todaysWeather = myObj.data.consolidated_weather[0];
      var tomorrowsWeather = myObj.data.consolidated_weather[1];
      var inTwoDaysWeather = myObj.data.consolidated_weather[2];

      // Data retrieved for today's weather of a city (state, low & high temp) */
      this.todaysWeatherState = todaysWeather["weather_state_name"];
      this.todaysLowTemp = Math.round((todaysWeather["min_temp"] * 9) / 5 + 32);
      this.todaysHighTemp = Math.round(
        (todaysWeather["max_temp"] * 9) / 5 + 32
      );
      this.todaysWindSpeed = Math.round(10 * todaysWeather["wind_speed"]) / 10;
      this.todaysHumidity = todaysWeather["humidity"];

      // Data retrieved for tomorrow's weather of a city
      this.tomorrowsWeatherState = tomorrowsWeather["weather_state_name"];
      this.tomorrowsLowTemp = Math.round(
        (tomorrowsWeather["min_temp"] * 9) / 5 + 32
      );
      this.tomorrowsHighTemp = Math.round(
        (tomorrowsWeather["max_temp"] * 9) / 5 + 32
      );
      this.tomorrowsWindSpeed =
        Math.round(10 * tomorrowsWeather["wind_speed"]) / 10;
      this.tomorrowsHumidity = tomorrowsWeather["humidity"];

      // Data retrieved for in two days weather of a city
      this.inTwoDaysWeatherState = inTwoDaysWeather["weather_state_name"];
      this.inTwoDaysLowTemp = Math.round(
        (inTwoDaysWeather["min_temp"] * 9) / 5 + 32
      );
      this.inTwoDaysHighTemp = Math.round(
        (inTwoDaysWeather["max_temp"] * 9) / 5 + 32
      );
      this.inTwoDaysWindSpeed =
        Math.round(10 * inTwoDaysWeather["wind_speed"]) / 10;
      this.inTwoDaysHumidity = inTwoDaysWeather["humidity"];

      this.loading = true;
    });
  }
};
</script>

<style scoped>
h1 {
  font-size: 30px;
  font-weight: 800;
  color: white;
  z-index: 2;
  position: absolute;
  left: 0;
  right: 0;
  margin-left: auto;
  margin-right: auto;
  top: 10px;
  box-shadow: 0px 1px 5px 5px;
  width: 200px;
}

a.router-link-active {
  display: flex;
  font-size: 60px;
  margin-left: 50px;
  margin-top: 50px;
  color: black;
  width: 40px;
  height: 20px;
}

.background {
  z-index: -2;
  min-height: 100%;
  min-width: 1024px;
  width: 100%;
  height: auto;
  position: fixed;
  top: 0;
  left: 0;
  opacity: 0.7;
}
</style>
