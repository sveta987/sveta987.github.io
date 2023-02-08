<template>
  <div class="widget md:w-[20%] m-auto mt-[50px] bg-[#839FCD] rounded-md">
    <div v-if="!isOpenSettings">
      <div v-if="isAskedForLocation" class="flex flex-col justify-center">
        <div v-if="!isClickedSearchButton">
          <h5 class="mx-[5%] mt-[15px] text-white text-xl">Please give access to your location, or click button on
            bellow.</h5>
          <button
              class="text-white px-[10px] py-[5px] border border-gray-600 bg-[#9398C4] mx-[30%] mb-[15px] rounded-md hover:bg-gray-100 hover:text-[#9398C4]"
              @click="searchFirstCity">Enter location
          </button>
        </div>
        <div v-else>
          <label for="first-location" class="mx-[5%] mt-[15px] text-white text-xl">Please enter city name</label>
          <input id="first-location" type="text" v-model="firstCity"
                 class="w-[93%] py-[10px] px-[5px] rounded-md border border-gray-200 m-[10px] bg-gray-200 text-[#9398C4]"
                 @keyup.enter="getFirstCity"/>
          <button
              class="text-white px-[10px] py-[5px] border border-gray-600 bg-[#9398C4] mx-[30%] mb-[15px] rounded-md hover:bg-gray-100 hover:text-[#9398C4]"
              @click="getFirstCity">
            Search city
          </button>
        </div>
      </div>
      <div v-else>
        <div class="flex justify-end">
          <SettingIcon class="text-white cursor-pointer mr-[5px] mt-[5px]" @click="openSettings"/>
        </div>
        <div class="weather-of-city" v-for="(city, index) of searchedCities" :key="index">
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
  components: {
    WeatherOfTheCity,
    Settings,
    SettingIcon
  },
  data() {
    return {
      searchedCities: [],
      isAskedForLocation: true,
      isClickedSearchButton: false,
      firstCity: null,
      isOpenSettings: false,
    }
  },
  methods: {
    searchFirstCity() {
      this.isClickedSearchButton = !this.isClickedSearchButton
    },
    getFirstCity() {
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
    openSettings() {
      this.isOpenSettings = !this.isOpenSettings
    },
    closeSettings() {
      this.isOpenSettings = !this.isOpenSettings
      if (this.searchedCities.length === 0) {
        this.isAskedForLocation = !this.isAskedForLocation;
      }
    },
    changeLocation(locations) {
      this.searchedCities = locations
    }
  },
  mounted() {
    if (navigator.geolocation) {
      const successCallback = (position) => {
        axios
            .get(`https://api.openweathermap.org/data/2.5/weather?lat=${position.coords.latitude}&lon=${position.coords.longitude}&appid=01698da9b05c6e3a89f85a90b8443b7f`)
            .then(
                (response) => {
                  if (!this.searchedCities.find(city => city.id === response.data.id)) {
                    this.searchedCities.push(response.data)
                  }
                })
            .then(() => {
              this.isAskedForLocation = false
              localStorage.setItem('selectedCities', JSON.stringify(this.searchedCities))
            })
            .catch(err => console.log(err))
      }
      const errorCallback = (error) => {
        console.log(error);
      };
      navigator.geolocation.getCurrentPosition(successCallback, errorCallback)
    } else {
      console.log("Geolocation is not supported by this browser.")
    }
    this.isAskedForLocation = JSON.parse(localStorage.getItem('isAskedForLocation'))
    this.searchedCities = localStorage.getItem('selectedCities') ? JSON.parse(localStorage.getItem('selectedCities')) : []
    if (!this.searchedCities || this.searchedCities.length === 0) {
      this.isAskedForLocation = !this.isAskedForLocation;
      localStorage.setItem('isAskedForLocation', this.isAskedForLocation)
    }
  }
}
</script>