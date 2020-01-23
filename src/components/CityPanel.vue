<template>
  <sui-button size="big" v-on:click="cityFound">{{ this.cityName }}</sui-button>
</template>

<script>
export default {
  name: "CityPanel",
  props: {
    cityName: {
      type: String
    }
  },
  data() {
    return {
      currCity: ""
    };
  },
  methods: {
    cityFound: function() {
      this.$emit("currentCity", this.cityName);
      localStorage.setItem("currCity", this.cityName);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.ui.button {
  display: flex;
  margin: 20px auto;
  width: 20rem;
  justify-content: center;
  height: 6rem;
  border: 1px solid #d0d1d2;
  background-color: white;
  font-weight: 800;
  color: #333333;
  cursor: pointer;
  box-shadow: none;
}

a {
  font-weight: 800;
  display: inline-block;
  line-height: 1em;
  overflow: hidden;
  width: 200px;
  color: #333333;
}

a:hover {
  color: #333333;
}

.ui.button.hover {
  color: #6f7072;
}
</style>
