<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 65 ? 'warm' : ''">
    <main>
      <div class="search-box">
        <input 
        type="text" 
        placeholder="Search City or Zip Code" 
        class="search-bar" 
        v-model="query"
        @keypress="fetchWeather"
        />

      </div>
      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location"> {{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{ currentDate }}</div>
        </div>
      </div>
      <div class="weather-box" v-if="typeof weather.main != 'undefined'">
        <div class="temp">{{ Math.round(weather.main.temp) }}°f</div>
        <div class="weather-icon"><img :src="'http://openweathermap.org/img/wn/' + weather.weather[0].icon + '@2x.png'" alt="Weather Icon"/></div>
        <div class="weather">{{ weather.weather[0].main }}</div>
        <br>
        <div class="temp_min_max">{{ weather.main.temp_min }}° {{ weather.main.temp_max }}°</div>
        <div class="humidity">Humidity: {{ weather.main.humidity }}%</div>
        <div class="wind-speed">Wind Speed: {{ weather.wind.speed }} mph</div>
        <div class="wind-deg">Wind Degree: {{ weather.wind.deg }}°</div>
        <div class="pressure">Pressure: {{ weather.main.pressure }} hPa</div>
        <div class="lastUpdate">{{ weather.lastupdate }}</div>
      </div>
    </main>
  </div>
</template>

<script>


const months = ["January", "February", "March", "April", "May", "June", "July",
                "August", "September", "October", "November", "December"];
const days = ["Sunday", "Monday", "Teusday", "Wednesday", "Thursday", "Friday", "Saturday"];

  export default {
    name: 'App',
    data () {
      return {
        api_key: '7ac6ca640bb50754cff30f22b8753722',
        url_base: 'https://api.openweathermap.org/data/2.5/',
        query: ' ',
        weather: {},
        temperatureUnit: 'F',
        saved_locations: []
      }
    },
    
    methods: {

      getCurrentLocation() {
        if ("geolocation" in navigator) {
          navigator.geolocation.getCurrentPosition((position) => {
            //Make a request to the OpenWeatherMap API using the user's latitude and longitude
            fetch(`${this.url_base}weather?lat=${position.coords.latitude}&lon=${position.coords.longitude}&units=imperial&APPID=${this.api_key}`)
            .then(res => {
              return res.json();
            }).then(this.setResults)
          })
        }
      },

      fetchWeather (e) {
        if (e.key == "Enter") {
          fetch(`${this.url_base}weather?q=${this.query}&units=imperial&APPID=${this.api_key}`)
            .then(res => {
              return res.json();
            }).then(this.setResults);
        }
      },

      setResults (results) {
        this.weather = results;
        this.query = results.name;
        this.saved_locations.push(results)
        console.log(this.saved_locations);
      }
    },

    created() {
      this.getCurrentLocation();
    },
  
    computed: {
      currentDate() {
            let current_date = new Date();
            let day = days[current_date.getDay()];
            let date = current_date.getDate();
            let month = months[current_date.getMonth()];
            let year = current_date.getFullYear();
            return `${day}, ${date} ${month}, ${year}`;
      }
    }
  }

</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Montserrat', sans-serif;
  background: #f2f2f2;
}

#app {
  background-image: url('./assets/cold-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4 seconds;
}

#app.warm {
  background-image: url(./assets/warm-bg.jpg);
}

main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
}


.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  color: #313131;
  font-size: 20px;

  appearance: none;
  border: none;
  outline: none;
  background: none;

  box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}

.location-box .location{
  color: white;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: white;
  font-size: 20px;
  font-weight: 300;
  text-align: center;
  font-style: italic;
}

.weather-box {
  text-align: center;
  align-items: center;
  overflow: hidden;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: white;
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25)
}

.weather-box .weather {
  color: white;
  font-size: 48px;
  font-weight: 00;
  font-style:italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);

}

.weather-box .temp_min_max {
  color: white;
  font-size: 20px;
  font-weight: 500;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);

}

.weather-box .humidity {
  color: white;
  font-size: 20px;
  font-weight: 500;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);

}

.weather-box .wind-speed {
  color: white;
  font-size: 20px;
  font-weight: 500;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);

}

.weather-box .pressure {
  color: white;
  font-size: 20px;
  font-weight: 500;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);

}

.weather-box .wind-deg {
  color: white;
  font-size: 20px;
  font-weight: 500;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);

}

.weather-box .lastUpdate {
  color: white;
  font-size: 20px;
  font-weight: 500;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);

}

.weather-box .weather-icon {
    transition: all 0.2s ease-in-out;
}
.weather-box .weather-icon:hover {
    transform: scale(1.2);
}

.weather-box .weather-icon {
    margin-right: 20px;
}

</style>
