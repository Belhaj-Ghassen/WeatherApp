<template>
  <div class="container p-0">
    <div class="d-flex">
      <div class="card main-div w-100">
        <div class="p-3">
          <h2 class="mb-1 day">Today</h2>
          <p class="text-light date mb-0">{{ date }}</p>
          <small>{{ time }}</small>
          <h2 class="place">
            <i class="fa fa-location">{{ name }}  <small>{{ country }}</small></i>
          </h2>
          <div class="temp">
            <h1 class="weather-temp">{{ temperature }}&deg;</h1>
            <h2 class="text-light">{{ description }} <img :src="iconUrl"></h2>
          </div>
        </div>
      </div>
      <div class="card card-2 w-100">
        <table class="m-4">
          <tbody>
            <tr>
              <th>Sea Level</th>
              <td v-if="sea_level >0">{{ sea_level }}</td>
              <td v-else>Null</td>
            </tr>
            <tr>
              <th>Humidity</th>
              <td>{{ humidity }}</td>
            </tr>
            <tr>
              <th>Wind</th>
              <td>{{ wind }}</td>
            </tr>
          </tbody>
        </table>
        <DaysWeather :cityname="cityname"></DaysWeather>
        <div id="div_Form" class="d-flex m-3 justify-content-center">
          <form action="">
            <input type="button" value="Change Location" @click="changeLocation" class="btn change_btn btn-primary">
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import DaysWeather from './DaysWeather.vue'
export default {
  name: 'myWeather',
  components: {
    DaysWeather,
  },
  props:{
    city:String,
  },
  data(){
    return {
      cityname:this.city,
      temperature:null,
      description:null,
      iconUrl:null,
      date:null,
      time:null,
      name:null,
      sea_level:null,
      wind:null,
      country:null,
      humidity:null,
      monthNames: [
        "January", "February", "March", "April", "May", "June",
        "July", "August", "September", "October", "November", "December"
      ],
    }
  },
  methods:{
    changeLocation() {
      window.location.reload();
    }
  },
  async created() {
    try {
      const response = await axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=f24f0b3e5728934b99cb0562dae46a0d`);
      const weatherData= response.data;
      this.temperature=Math.round(weatherData.main.temp);
      this.description=weatherData.weather[0].description;
      this.name=weatherData.name;
      this.sea_level=weatherData.main.sea_level;
      this.wind=weatherData.wind.speed;
      this.country=weatherData.sys.country;
      this.humidity=weatherData.main.humidity;
      this.iconUrl=`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`;
      const d=new Date();
      this.date =d.getDate() + '-' + this.monthNames[d.getMonth()] + '-' + d.getFullYear();
      this.time=d.getHours() + ':' + d.getMinutes() + ':' + d.getSeconds();
      console.log(response.data);
    } catch (error) {
      console.error("There was an error fetching the weather data:", error);
    }
  }

  
};
</script>

<style scoped>
body {
  background-color: #343d4b;
}
.weather-temp {
  margin: 0;
  font-weight: 700;
  font-size: 4em;
}
h2.mb-1.day {
  font-size: 3rem;
  font-weight: 400;
}
.main-div {
  border-radius: 20px;
  color: #fff;
  background-image: url("https://i.pinimg.com/736x/a0/9f/df/a09fdf03564c3013acdbf96306fc7735.jpg");
  background-size: cover;
  background-position: center;
  background-blend-mode: overlay;
  background-color: rgba(0, 0, 0, 0.5);
  background-repeat: no-repeat;
  position: relative;
}
.temp {
  position: absolute;
  bottom: 0;
}
.main-div:hover {
  transform: scale(1.1);
  transition: transform 0.5s ease;
  z-index: 1;
}
.card-2 {
  background-color: #212730;
  border-radius: 20px;
}
.card-details {
  margin-left: 19px;
}
.h1_left {
  position: absolute;
  bottom: 25px;
  left: 16px;
  font-size: 3vw;
  line-height: 1.2;
}
.h3_left {
  position: absolute;
  left: 16px;
  font-size: 2vw;
  line-height: 0.5;
}
.h3_left small {
  font-size: 1rem;
}
table {
  position: relative;
  left: 15px;
  border-collapse: separate;
  border-spacing: 15px;
  width: 85%;
  text-align: left;
  max-width: 600px;
  margin: 0 auto;
}
th,
td {
  font-size: 18px;
  color: #fffefe;
}
td {
  text-align: right;
}
table,
tr:hover {
  color: red;
}
.change_btn {
  background: linear-gradient(to right, cyan, magenta);
  border: none;
  color: white;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
  border-radius: 5px;
}
.change_btn:hover {
  transform: scale(0.9);
  transition: transform 0.1s ease;
}

</style>
