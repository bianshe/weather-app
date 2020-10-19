<template>
  <div>
    <h1>Weather App</h1>

    <div class="message-div">
      <p v-if="locError || dataError">
        Sorry, but the following errors occurred:
        {{ locError + " " + dataError }}
      </p>
      <em v-if="gettingLocation">Getting your location...</em>
      <em v-if="gettingData">Loading weather data...</em>
    </div>
    <div v-if="location && weatherData" class="weather-card">
      <div class="location">
        <p>
          {{ weatherData.name }}<span v-if="country">, {{ country }}</span>
        </p>
      </div>
      <div class="weather-data">
        <div>
          <div class="weather-icon">
            <img
              :src="
                `http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@4x.png`
              "
            />
          </div>
          <div>
            <span
              >{{ weatherData.weather[0].main }}:
              {{ weatherData.weather[0].description }}</span
            >
          </div>
          <div class="weather-stats">
            <div>
              <span>T: {{ weatherData.main.temp | decimal }} °</span>
            </div>
            <div>
              <span>H: {{ weatherData.main.humidity | decimal }} %</span>
            </div>
            <div>
              <span
                class="wind-dir"
                :style="'transform: rotate(' + weatherData.wind.deg + 'deg)'"
              ></span>
              <span>{{ weatherData.wind.speed | decimal }} m/s</span>
            </div>
            <div>
              <span>P: {{ weatherData.main.pressure }} hPa</span>
            </div>
          </div>
        </div>
      </div>
      <p>Last Update: {{ updDate }}</p>
      <button v-on:click="getLocation">Update</button>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Weather",
  data() {
    return {
      latitude: null,
      longtitude: null,
      location: null,
      locError: "",
      dataError: "",
      gettingLocation: false,
      gettingData: false,
      country: "",
      updDate: "",
      weatherData: null,
    };
  },
  filters: {
    decimal(value) {
      return value.toFixed(1);
    },
  },
  mounted() {
    this.getLocation();
  },
  methods: {
    getLocation() {
      if (!("geolocation" in navigator)) {
        this.locError = "Geolocation is not available.";
        return;
      }
      this.gettingLocation = true;
      navigator.geolocation.getCurrentPosition(
        (position) => {
          this.locError = "";
          this.dataError = "";
          this.gettingLocation = false;
          this.location = position;
          this.latitude = parseFloat(position.coords.latitude).toFixed(2);
          this.longitude = parseFloat(position.coords.longitude).toFixed(2);
          this.getWeatherData();
        },
        (error) => {
          this.gettingLocation = false;
          this.locError = error.message;
        }
      );
    },
    getWeatherData() {
      this.gettingData = true;
      axios
        .get(
          "https://cors-anywhere.herokuapp.com/http://api.openweathermap.org/data/2.5/weather?lat=" +
            this.latitude +
            "&lon=" +
            this.longitude +
            "&appid=13098658eeecc73038d25caecacc7eba&units=metric"
        )
        .then(
          (response) => (
            (this.weatherData = response.data),
            (this.country = response.data.sys.country),
            (this.updDate = new Date().toLocaleString())
          )
        )
        .catch((error) => {
          console.log(error);
          this.dataError = error.message;
        })
        .finally(() => (this.gettingData = false));
    },
  },
};
</script>

<style scoped>
.weather-card {
  display: block;
  margin: 0 auto;
  padding: 20px;
  background-color: #cccccc;
  max-width: 400px;
  text-align: center;
}

.location {
  font-size: 22px;
}

.weather-data {
  font-size: 20px;
  line-height: 30px;
}

.weather-icon img {
  width: 100px;
}

.weather-stats {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-evenly;
}

.weather-stats div {
  width: 50%;
}

.weather-stats .location {
  font-size: 30px;
}

.wind-dir {
  display: inline-block;
  width: 1em;
  height: 1em;
  background: url("https://www.metaweather.com/static/img/windarrow.svg");
  background-size: 100% 100%;
  margin-right: 5px;
}

.message-div {
  height: 25px;
}

button {
  background-color: #2c3e50;
  color: white;
  font-size: 1.1rem;
  border: none;
  padding: 5px 10px;
}
</style>
