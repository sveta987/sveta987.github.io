<template>
  <div class="flex justify-center text-center text-2xl text-white border-b border-b-white w-[95%] ml-[2.5%] my-[10px]">
    <h3 class="py-[10px]">{{ city.name }}, {{ city.sys.country }}</h3>
  </div>
  <div class="flex justify-evenly border-b border-b-white w-[95%] ml-[2.5%] mb-[10px]">
    <div class="mt-[5%]">
      <img :src=" `http://openweathermap.org/img/wn/${city.weather[0].icon}@2x.png`" alt="weather-icon">
      <h5 class="capitalize text-white text-center text-xl">{{ city.weather[0].description }}</h5>
    </div>
    <div class="text-center text-white">
      <div>
        <div class="border-b border-b-white pb-[5px] mb-[5px]">
          <p class="text-[15px]">{{ date }}</p>
          <h2 class="text-5xl text-white text-center">{{ time }}</h2>
        </div>
        <div>
          <h2 class="text-6xl bolder-b border-b-white">{{ kelvinToCelsius(city.main.temp) }}°C</h2>
        </div>
      </div>
    </div>
  </div>
  <div class="text-[10px] text-white flex justify-evenly mb-[10px] pb-[15px] border-b border-dashed border-b-white">
    <div>
      <p>Feels like: {{ kelvinToCelsius(city.main.feels_like) }}°</p>
      <p>Humidity: {{ city.main.humidity }}%</p>
      <p>Wind: {{ city.wind.speed }}m/s</p>
    </div>
    <div>
      <p>Pressure: {{ city.main.pressure }} hPa</p>
      <p>Visibility: {{ (city.visibility / 1000).toFixed(1) }}km</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "WeatherOfTheCity",
  props: ['city'],
  data() {
    return {
      time: null,
      date: null,
    }
  },

  methods: {
    kelvinToCelsius(kelvin) {
      return Math.floor(kelvin - 273.15)
    },
    getTime() {
      const months = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"]
      const dayNames = ['Mon', 'Tues', 'Wed', 'Thurs', 'Fri', 'Sat', 'Sun']
      let d = new Date();

      let utc = d.getTime() + (d.getTimezoneOffset() * 60000)
      let nd = new Date(utc + (3600000 * (this.city.timezone / 3600)))

      this.time = nd.getHours() + ":" + nd.getMinutes()
      this.date = months[nd.getMonth()] + ' ' + nd.getDate() + ' ' + nd.getFullYear() + ' ' + dayNames[nd.getDay() - 1]
    }
  },
  mounted() {
    this.getTime()
    setInterval(this.getTime, 1000)
  }
}
</script>
