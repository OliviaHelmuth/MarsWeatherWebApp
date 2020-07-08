<template>
  <div id="background">
    <div id="head">
      <div>Mars Weather at Elysium Planitia</div>
      <div>current season: {{season}}</div>
      <!-- <div>{{$moment().format()}}</div> -->
    </div>
    <div id="flightBooking">
      book a flight to
      <a href="https://www.spacex.com/rideshare/" target="_blank">Mars !</a>
    </div>
    <div id="weatherForecast">
      <div class="weatherDay" v-for="item in days" :key="item.day">
        <div class="dayWrapper"><span class="day">Sol {{item.day}}</span></div>
        <span class="temp">min: {{item.min}}°C</span>
        <span class="temp">max: {{item.max}}°C</span>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      apiData: null,
      days: [],
    };
  },
  computed: {
    season() {
      if (this.apiData == null) {
        return "loading..."
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
#background {
  padding: 4rem;
  background-image: url("../pictures/marspicture.jpg");
  background-size: cover;
  height: 100vh;
}
#head {
  font-weight: 900;
  font-size: 4.5rem;
}
#weatherForecast {
  margin-top: 20rem;
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  column-gap: 1rem;
  justify-items: center;
  .weatherDay {
    padding-top: 1rem;
    border-radius: 10px;
    height: 13rem;
    width: 11rem;
    background-color: rgba(165, 153, 140, 0.3);
  }
  .dayWrapper {
    height: 4rem;
  }
}
#flightBooking {
  margin-top: 3rem;
  font-size: 2.1rem;
  a {
    text-decoration: none;
  }
  a:hover {
    filter: brightness(70%);
  }
}
.day {
  font-size: 2rem;
  text-align: center;
  padding: 2rem;
}
.temp {
  font-size: 1.5rem;
  padding: 1rem;
}
</style>
