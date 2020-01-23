<template>
  <div class="weather">
    <div v-if="!this.loading">
      <div class="loading" v-if="this.first">Loading...</div>
    </div>
    <div v-if="this.loading">
      <sui-card>
        <sui-card-content>
          <sui-card-header>{{ this.day }}</sui-card-header>
          <sui-card-meta>{{ this.weatherState }}</sui-card-meta>
          <sui-card-meta> Wind Speed: {{ this.windSpeed }}mph </sui-card-meta>
          <sui-card-meta> Humidity: {{ this.humidity }}% </sui-card-meta>
          <sui-card-description
            >Low: {{ this.low }}°F High: {{ this.high }}°F</sui-card-description
          >
          <sui-card-description>
            <img v-bind:src="this.icon" alt="Icon" />
          </sui-card-description>
        </sui-card-content>
      </sui-card>
    </div>
  </div>
</template>

<script>
export default {
  name: "WeatherPanel",
  data: function() {
    return {
      id: 0
    };
  },
  props: {
    day: {
      type: String
    },
    weatherState: {
      type: String
    },
    high: {
      type: Number
    },
    low: {
      type: Number
    },
    windSpeed: {
      type: Number
    },
    humidity: {
      type: Number
    },
    loading: {
      type: Boolean
    },
    first: {
      type: Boolean
    },
    icon: {
      type: String
    },
    backgroundCity: {
      type: String
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.ui.card {
  display: flex;
  margin: 40px auto;
  justify-content: center;
  border: 0.5px solid #d0d1d2;
  box-shadow: none;
}
.loading {
  font-size: 28px;
  font-weight: 700;
  margin: 80px auto;
}

img {
  margin-top: 5px;
  width: 40px;
  height: 40px;
}
</style>
