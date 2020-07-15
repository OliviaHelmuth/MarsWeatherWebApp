<template>
  <div id="background">
    <div id="head">
      <div>Mars Weather at Elysium Planitia</div>
      <div>current season: {{season}}</div>
    </div>
    <div id="flightBooking">
      book a flight to
      <a href="https://www.spacex.com/rideshare/" target="_blank">Mars !</a>
    </div>
    <div class="flex-wrapper">
      <div id="weatherForecast">
        <div class="weatherDay" v-for="item in days" :key="item.day">
          <div class="day">Sol {{item.day}}</div>
          <div class="temp">min: {{item.min}}°C</div>
          <div class="temp">max: {{item.max}}°C</div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      apiData: null,
      days: []
    };
  },
  computed: {
    season() {
      if (this.apiData == null) {
        return "loading...";
      }
      const season = this.apiData["sol_keys"][0];
      return this.apiData[season]["Season"];
    }
  },
  methods: {
    compute() {
      this.$axios
        .get(
          "https://api.nasa.gov/insight_weather/?api_key=DEMO_KEY&feedtype=json&ver=1.0"
        )
        .then(response => {
          this.apiData = response.data;
          this.displayData();
        });
    },
    addDay(day, min, max) {
      this.days.push({ day: day, min: min, max: max });
    },
    displayData() {
      for (let i = 0; i < 7; i++) {
        const day = this.apiData["sol_keys"][i];
        const low = Math.round(this.apiData[day]["AT"]["mn"] * 10) / 10;
        const high = Math.round(this.apiData[day]["AT"]["mx"] * 10) / 10;
        this.addDay(day, low, high);
      }
    }
  },
  mounted() {
    this.compute();
  }
};
</script>
<style lang="scss">
$white: #f2f1ec;
$font-big: 1.5rem;
$font-normal: 1rem;
$font-small: 0.75rem;

$spacing-sm: 0.5rem;
$spacing: 1rem;
$spacing-lg: 1.5rem;

@font-face {
  font-family: "regular";
  src: url("../fonts/dinpro-regular-cufonfonts-webfont/DINPro-Regular_13937.woff")
    format("woff");
}
* {
  box-sizing: border-box;
  font-family: regular;
  color: $white;
}
body {
  background-attachment: fixed;
  background-image: url(../pictures/marspicture.jpg);
  background-size: cover;
}
#background {
  display: flex;
  flex-direction: column;
  padding: 4rem;
  height: 100vh;
}
#head {
  font-weight: 900;
  font-size: $font-big;
}
.flex-wrapper {
  display: flex;
  flex: 1;
  align-items: end;
}
#weatherForecast {
  width: 100%;
  display: grid;
  grid-template-columns: repeat(auto-fit, 10rem);
  column-gap: 1rem;
  row-gap: 1rem;
  .weatherDay {
    padding: $spacing-lg;
    border-radius: 10px;
    background-color: rgba(165, 153, 140, 0.3);
  }
}
#flightBooking {
  margin: 3rem 0;
  font-size: $font-big;
  a {
    text-decoration: none;
  }
  a:hover {
    filter: brightness(50%);
    text-decoration: underline;
  }
}
.day {
  font-weight: bold;
  font-size: $font-big;
  text-align: center;
}
.temp {
  font-size: $font-normal;
  padding: $spacing-sm;
  text-align: center;
}

@media (max-width: 463px) {
  #weatherForecast {
    display: block;
    .weatherDay {
      margin: $spacing;
    }
  }
  #background {
    padding: 3rem;
  }
}
</style>
