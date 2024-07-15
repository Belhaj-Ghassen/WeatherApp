<template>
    <div class="days-tab text-center">
      <div v-if="loading" class="loading">Loading...</div>
      <ul v-else class="p-0">
        <li v-for="day in forecast" :key="day.date" class="li_active" >
          <div class="py-3"><img :src="day.iconUrl"></div>
          <div class="py-3">{{ getDayNAme(day.date) }}</div>
          <div class="py-3">{{ day.temperature }}&deg;C</div>
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  import moment from 'moment';
  export default {
    name: 'DaysWeather',
    props:{
        cityname:String,
    },
    data(){
        return{
            forecast: [],
            loading: true,
            iconUrl: null,
        }
    },
    mounted(){
        this.fetchweatherData();
    },
    methods:{
        async fetchweatherData(){
            const apiKey=`f24f0b3e5728934b99cb0562dae46a0d`;
            const city=this.cityname;
            const apiUrl=`https://api.openweathermap.org/data/2.5/forecast?q=${city}&units=metric&appid=${apiKey}`;
            
            await axios.get(apiUrl).then(Response => {
                const forecastData = Response.data.list;
                const filteredData = forecastData.map(item => {
                    return {
                        date :moment (item.dt_txt.split(' ')[0]),
                        temperature: Math.round(item.main.temp),
                        description :item.weather[0].description,
                        iconUrl: `https://api.openweathermap.org/img/w/${item.weather[0].icon}.png`,
                        };
                    }).reduce((acc, item) => {
                        if(!acc.some (day => day.date.isSame (item.date, 'day'))){
                            acc.push(item);
                        }
                        return acc;
                    }, []).slice(1, 5);
                    console.log(filteredData, "working");
                    this.forecast=filteredData;
                    this.loading=false;
                }).catch(error => {
                    console.error('Error fetching weather data:', error);
                    this.loading = false;
                });
        },
        getDayNAme(date){
            return date.format('ddd');

        }
    }
  };
  </script>
  
  <style scoped>
  .days-tab {
    width: 90%;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2), 0 6px 20px rgba(0, 0, 0, 0.19);
    border-radius: 20px;
    margin: auto;
    background: #21272d;
  }
  .loading {
    color: #fff;
  }
  ul {
    margin: 0;
    padding: 0;
  }
  li {
    display: inline-block;
    list-style: none;
    height: 100%;
    width: 21%;
    max-width: 21%;
    font-size: 1vw;
    line-height: 1.2;
  }
  span {
    display: block;
    margin-bottom: 5px;
    font: 100% sans-serif;
    height: 35px;
  }
  .li_active {
    background: #253d5c;
    color: #fff;
    border-radius: 10px;
    margin: 0.5rem;
    font-weight: 600;
    transition: transform 0.1s ease;
  }
  .li_active:hover {
    transform: scale(1.2);
  }
  .li_active_temp {
    display: inline-block;
    background-color: #222831;
    color: #ffffff;
    transition: background-color 0.5s, transform 0.1s ease;
    border-radius: 10px;
  }
  .li_active_temp:hover {
    transform: scale(1.2);
    background: #fff;
    color: #191a1f;
  }
  </style>
  