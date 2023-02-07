<template>
<div class="flex justify-between text-white text-xl p-[5px]">
  <h4>Settings</h4>
  <p class="cursor-pointer" @click="closeSettings">X</p>
</div>
<div>
  <draggable v-model="items" v-if="items.length > 0 && visible">
    <template v-slot:item="{item}">
      <div class="bg-[#A2BDDE] shadow-xl mt-[10px] flex justify-between mx-[5px] rounded-md text-white">
        <div class="flex align-baseline py-[5px]">
          <menu-icon class="m-[5px]"/>
          <h3>{{ item.name }}, {{ item.sys.country }}</h3>
        </div>
        <button @click="()=>{deleteCity(item.index)}">
          <TrashIcon class="m-[5px]"/>
        </button>
      </div>
    </template>
  </draggable>
</div>

  <div class="flex flex-col justify-center">
    <label for="first-location" class="mx-[5%] mt-[15px] text-white text-xl text-center">Add location</label>
    <input id="first-location" type="text" v-model="searchedCity" class="w-[93%] p-[5px] rounded-md border border-gray-200 m-[10px] bg-gray-200 text-[#9398C4]" @keyup.enter="searchCity"/>
    <button class="text-white px-[10px] py-[5px] border border-gray-600 bg-[#9398C4] mx-[30%] mb-[15px] rounded-md hover:bg-gray-100 hover:text-[#9398C4]" @click="searchCity">
      Search city
    </button>
  </div>
</template>

<script>
import MenuIcon from 'vue-ionicons/dist/md-menu.vue'
import TrashIcon from 'vue-ionicons/dist/md-trash.vue'
import SubmitIcon from 'vue-ionicons/dist/md-arrow-back.vue'
import Draggable from "vue3-draggable"
import axios from "axios";

export default {
  name: "Settings",
  props: ['cities'],
  emits: ['closeSettings', 'deleteCity', 'changeLocation'],
  components: {MenuIcon, TrashIcon, SubmitIcon, Draggable},
  data() {
    return {
      items: [],
      searchedCity: null,
      localCities: [],
      visible: true
    }
  },
  watch: {
    items: {
      handler() {
        this.changeLocations()
        localStorage.setItem('selectedCities', JSON.stringify(this.items))
      },
      deep: true,
      immediate: true,
    }
  },
  methods: {
    closeSettings() {
      this.$emit('closeSettings')
    },
    async deleteCity(index) {
      this.items.splice(index, 1)
      this.changeLocations()
      this.visible = false
      await this.$nextTick(() => {
        this.visible = true
      })
      if(this.items.length === 0){
        localStorage.removeItem('isAskedForLocation')
      }
    },
    changeLocations() {
      this.$emit('changeLocation', this.items)
    },
    async searchCity() {
      if (this.searchedCity.trim()) {
        axios
            .get(`https://api.openweathermap.org/data/2.5/weather?q=${this.searchedCity}&appid=01698da9b05c6e3a89f85a90b8443b7f`)
            .then(
                (response) => {
                  if (!this.items.find(item => item.id === response.data.id)) {
                    this.items.push(response.data)
                  }
                })
            .then(() => {
              this.changeLocations()
              this.visible = false
              this.$nextTick(() => {
                this.visible = true
              })
              this.searchedCity = ''
            })
            .catch(err => console.log(err))
      }
    }
  },
  beforeMount() {
    this.items = this.cities
  },
  mounted() {
    this.items = JSON.parse(localStorage.getItem('selectedCities'))
  }
}
</script>