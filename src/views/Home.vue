<template>
  <div class="home">
    <h1>Weather</h1>
    <!-- Image Source : https://cdn.hipwallpaper.com/i/98/64/Cb94iV.jpg -->
    <img class="homeBackground" v-bind:src="imageSource()" alt="Home Background" />
    <div class="routers">
      <router-link :to="{ name: 'city', params: { woeid: '2487956' } }">
        <CityPanel :cityName="firstCity" />
      </router-link>

      <router-link :to="{ name: 'city', params: { woeid: '2459115' } }">
        <CityPanel :cityName="secondCity" />
      </router-link>

      <router-link :to="{ name: 'city', params: { woeid: '2367105' } }">
        <CityPanel :cityName="thirdCity" />
      </router-link>

      <router-link :to="{ name: 'city', params: { woeid: '2357536' } }">
        <CityPanel :cityName="fourthCity" />
      </router-link>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import CityPanel from "@/components/CityPanel.vue";

export default {
  name: "home",
  components: {
    CityPanel
  },
  data: function() {
    return {
      firstCity: "San Francisco, CA",
      secondCity: "New York, NY",
      thirdCity: "Boston, MA",
      fourthCity: "Austin, TX"
    };
  },
  methods: {
    // Inspiration taken from online research
    imageSource: function() {
      if (this.currCity != "") {
        var images = require.context("../assets/", false, /\.jpg$/);
        return images("./weather2.jpg");
      }
    }
  }
};
</script>

<style scoped>
h1 {
  font-weight: 800;
  font-size: 4.5vw;
  padding-top: 50px;
  margin-bottom: 80px;
  color: white;
  box-shadow: 0px 1px 10px 5px;
}

.routers {
  display: block;
  width: 280px;
  margin: 0 auto;
}

.homeBackground {
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
