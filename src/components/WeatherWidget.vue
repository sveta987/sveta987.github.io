<template>
<div class="widget md:w-[20%] m-auto mt-[50px] bg-[#839FCD] rounded-md">
  <div v-if="!isOpenSettings">
    <div v-if="isAskedForLocation" class="flex flex-col justify-center">
      <label for="first-location" class="mx-[5%] mt-[15px] text-white text-xl">Please enter your city name</label>
      <input id="first-location" type="text" v-model="firstCity" class="w-[93%] py-[10px] px-[5px] rounded-md border border-gray-200 m-[10px] bg-gray-200 text-[#9398C4]" @keyup.enter="getFirstCity"/>
      <button class="text-white px-[10px] py-[5px] border border-gray-600 bg-[#9398C4] mx-[30%] mb-[15px] rounded-md hover:bg-gray-100 hover:text-[#9398C4]" @click="getFirstCity">
        Search city
      </button>
    </div>
    <div v-else>
      <div class="flex justify-end">
        <SettingIcon class="text-white cursor-pointer" @click="openSettings"/>
      </div>
      <div class="weather-of-city " v-for="(city, index) of searchedCities" :key="index">
        <weather-of-the-city :city="city"/>
      </div>
    </div>
  </div>
  <div v-else>
    <settings :cities="searchedCities" @closeSettings="closeSettings" @changeLocation="changeLocation"/>
  </div>
</div>
</template>

<script>
import axios from "axios";
import WeatherOfTheCity from "@/components/WeatherOfTheCity"
import Settings from "@/components/Settings"
import SettingIcon from 'vue-ionicons/dist/md-settings.vue'

export default {
  name: "WeatherWidget",
  components:{
    WeatherOfTheCity,
    Settings,
    SettingIcon
  },
  data(){
    return{
      searchedCities: [],
      isAskedForLocation: true,
      firstCity: null,
      isOpenSettings: false
    }
  },
  methods:{
    getFirstCity(){
      if (this.firstCity.trim()) {
        axios
            .get(`https://api.openweathermap.org/data/2.5/weather?q=${this.firstCity}&appid=01698da9b05c6e3a89f85a90b8443b7f`)
            .then(
                (response) => {
                    this.searchedCities.push(response.data)
                })
            .then(() => {
              this.firstCity = ''
              this.isAskedForLocation = false
              localStorage.setItem('selectedCities', JSON.stringify(this.searchedCities))
            })
            .catch(err => console.log(err))
      }
    },
    openSettings(){
      this.isOpenSettings = !this.isOpenSettings
    },
    closeSettings(){
      this.isOpenSettings = !this.isOpenSettings
    },
    changeLocation(locations){
      this.searchedCities = locations
    }
  },
  mounted() {
    this.searchedCities = JSON.parse(localStorage.getItem('selectedCities'))
    this.isAskedForLocation = JSON.parse(localStorage.getItem('isAskedForLocation'))
    if (this.searchedCities.length === 0){
      this.isAskedForLocation = !this.isAskedForLocation;
      localStorage.setItem('isAskedForLocation',this.isAskedForLocation )
    }
  }
}
</script>