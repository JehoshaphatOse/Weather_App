<script setup>
import { ref, onMounted, watch } from 'vue'
import Loader from './components/Loader.vue'
import Home from './components/Home.vue'
const showSearchedCities = ref(false) // Start as  hidden
const isInitialView = ref(true)
const Search = ref('')
const fetchedData = ref([])
const errormessage = ref('')
const apiUrl = ref('https://api.weatherapi.com/v1/current.json?key=1243f23a41df434aa6a150725232809')
const searchedCityData = ref()
const loader = ref(false)
const latitude = ref()
const longitude = ref()
const apiUrl1 = ref('https://api.weatherapi.com/v1/current.json?')
const apikey = ref('1243f23a41df434aa6a150725232809')
const currentCityData = ref()
const isFahrenheit = ref()
const degreeCelcius = ref()

const switchInitialView = () => {
  isInitialView.value = false
  fetchWeather()
}
// loader function

const showLoader = () => {
  loader.value = true
}
const hideLoader = () => {
  loader.value = false
}

// error message function

const setErrorMessage = (value) => {
  errormessage.value = value
}

// Function to hide the element when the window is loaded
const setShowSearchedCities = (value) => {
  showSearchedCities.value = value
}

// fetching popular cities

async function fetchWeather(city) {
  showLoader()
  let Portresponse = await fetch(
    'https://api.weatherapi.com/v1/current.json?key=1243f23a41df434aa6a150725232809&q=Nigeria(Port-Harcourt)'
  )
  let Londonresponse = await fetch(
    'https://api.weatherapi.com/v1/current.json?key=1243f23a41df434aa6a150725232809&q=London&aqi=no'
  )
  let newYorkresponse = await fetch(
    'https://api.weatherapi.com/v1/current.json?key=1243f23a41df434aa6a150725232809&q=America(New York)&aqi=no'
  )
  hideLoader()
  const Portdata = await Portresponse.json()
  const Londondata = await Londonresponse.json()
  const newYorkdata = await newYorkresponse.json()
  fetchedData.value = [Portdata, Londondata, newYorkdata]
  console.log(fetchedData.value)
}
// onMounted(async () => {
//   await fetchWeather()
// })

async function fetchSearchedCityWeather() {
  console.log(Search.value)
  showLoader()
  let response = await fetch(`${apiUrl.value}&q=${Search.value}`)
  hideLoader()

  if (response.status === 400) {
    errormessage.value = true

    Search.value = ''
  } else {
    let data = await response.json()
    searchedCityData.value = data
    console.log(searchedCityData.value)
    Search.value = ''
    setShowSearchedCities(true)
    errormessage.value = false
  }
}

const viewCurrentCityWeather = () => {
  navigator.geolocation.getCurrentPosition((data) => {
    latitude.value = data.coords.latitude
    longitude.value = data.coords.longitude
    console.log(longitude.value)
    console.log(latitude.value)
    fetchCurrentCityWeather()
    async function fetchCurrentCityWeather() {
      showLoader()
      if (latitude.value !== undefined && longitude.value !== undefined) {
        let response = await fetch(
          `${apiUrl1.value}q=${latitude.value},${longitude.value}&key=${apikey.value}`
        )
        hideLoader()
        let data = await response.json()
        searchedCityData.value = data
        console.log(data)
      }
    }
  })
  viewPopularCities()
}
const viewPopularCities = (city) => {
  searchedCityData.value = city
  setShowSearchedCities(true)
  errormessage.value = false
}
const changeUnit = () => {
  if (isFahrenheit) {
    const unit = parseInt(degreeCelcius.value.textContent)
    isFahrenheit.value = Math.round(unit * (9 / 5) + 32)
    console.log(isFahrenheit.value)
  }
}
</script>
<template>
  <Loader v-if="loader" />
  <Home v-if="isInitialView" @switch-view="switchInitialView()" />

  <main v-if="!isInitialView" class="bg-[#f8f7fc] w-fit md:w-full p-10">
    <div>
      <div class="flex items-center justify-between gap-[200px]">
        <button @click="setShowSearchedCities(false)">Home</button>
        <div class="flex-1 flex flex-col">
          <input
            :class="{ inputErr: errormessage }"
            @keypress="setErrorMessage(false)"
            @keypress.enter="fetchSearchedCityWeather()"
            v-model.trim="Search"
            id="Search"
            class="p-2 border-2 border-blue-200 rounded-full focus:outline-none flex-1"
            type="text"
            placeholder="Search a City"
          />
          <p v-if="errormessage" class="text-xs text-red-600">Invalid City Name</p>
        </div>

        <p @click="Search = ''" class="font-light cursor-pointer">Cancel</p>
      </div>

      <main class="Weather_container" v-if="showSearchedCities">
        <div class="flex justify-center m-4">
          <div>
            <div>
              <h1 id="cityName4" class="text-4xl font-semibold text-center">
                {{ searchedCityData.location.name }}
              </h1>
              <p id="country" class="text-yellow-500 font-semibold text-center mt-2">
                {{ searchedCityData.location.country }}
              </p>
              <p id="time" class="opacity-40 font-semibold text-center mt-2">
                {{ searchedCityData.location.localtime }}
              </p>
              <p id="condition4" class="font-sm text-center text-blue-400">
                {{ searchedCityData.current.condition.text }}
              </p>
            </div>
            <div class="mt-4 flex items-center justify-center">
              <img
                id="WeatherIcon4"
                class="rounded-full w-32"
                :src="searchedCityData.current.condition.icon"
                alt=""
              />
            </div>
            <div class="flex items-center justify-between">
              <h1
                ref="degreeCelcius"
                id="Temp4"
                class="text-3xl font-bold text-center mt-4 text-yellow-500 cursor-pointer"
              >
                {{ Math.round(searchedCityData.current.temp_c) }}&#8451;
              </h1>
              <svg
                @click="changeUnit"
                class="mt-4"
                xmlns="http://www.w3.org/2000/svg"
                xml:space="preserve"
                width="26"
                height="26"
                viewBox="0 0 511 511"
              >
                <path
                  d="M463.498 151H487.5c4.687 0 8.5 3.813 8.5 8.5s-3.813 8.5-8.5 8.5H143.503a7.5 7.5 0 0 0 0 15H487.5c12.958 0 23.5-10.542 23.5-23.5S500.458 136 487.5 136h-24.002a7.5 7.5 0 0 0 0 15z"
                />
                <path
                  d="M.059 161.178a23.513 23.513 0 0 0 6.615 14.724c.106.114.216.225.33.332l95.878 95.878c4.436 4.442 10.338 6.888 16.619 6.888s12.183-2.446 16.616-6.884c4.439-4.439 6.884-10.34 6.884-16.616s-2.445-12.176-6.884-16.616L80.235 183h31.261a7.5 7.5 0 0 0 0-15H62.129a7.5 7.5 0 0 0-5.304 12.803l68.684 68.688c1.606 1.606 2.491 3.74 2.491 6.009s-.885 4.403-2.494 6.013c-1.602 1.604-3.735 2.487-6.006 2.487s-4.404-.883-6.009-2.491l-96-96-.018-.018a8.398 8.398 0 0 1-2.447-5.303l-.005-.086a8.474 8.474 0 0 1 2.068-6.183c.134-.156.27-.306.484-.515l95.921-95.917C115.096 55.883 117.229 55 119.5 55s4.404.883 6.009 2.491c3.313 3.313 3.313 8.705 0 12.019l-68.684 68.688a7.499 7.499 0 0 0 5.304 12.803H431.5a7.5 7.5 0 0 0 0-15H80.235l55.881-55.884c9.162-9.162 9.162-24.069.003-33.228C131.683 42.446 125.781 40 119.5 40s-12.183 2.446-16.616 6.884L7.036 142.73c-.454.442-.891.909-1.308 1.396a23.39 23.39 0 0 0-5.533 12.347 23.72 23.72 0 0 0-.146 4.543c.002.054.006.108.01.162zM510.941 349.822a23.513 23.513 0 0 0-6.615-14.724 6.887 6.887 0 0 0-.33-.332l-95.878-95.878C403.683 234.446 397.781 232 391.5 232s-12.183 2.446-16.616 6.884c-4.439 4.439-6.884 10.34-6.884 16.616s2.445 12.176 6.884 16.616L430.765 328h-31.261a7.5 7.5 0 0 0 0 15h49.367a7.5 7.5 0 0 0 5.304-12.803l-68.684-68.688c-1.606-1.606-2.491-3.74-2.491-6.009s.885-4.403 2.494-6.013c1.602-1.604 3.735-2.487 6.006-2.487s4.404.883 6.009 2.491l96 96 .018.018a8.398 8.398 0 0 1 2.447 5.303l.005.086a8.474 8.474 0 0 1-2.068 6.183c-.134.156-.27.306-.484.515l-95.921 95.917c-1.602 1.604-3.735 2.487-6.006 2.487s-4.404-.883-6.009-2.491c-1.606-1.606-2.491-3.74-2.491-6.009s.885-4.403 2.491-6.009l68.684-68.688A7.499 7.499 0 0 0 448.871 360H95.5a7.5 7.5 0 0 0 0 15h335.265l-55.881 55.884c-4.439 4.439-6.884 10.34-6.884 16.616s2.445 12.176 6.881 16.612c4.436 4.442 10.338 6.888 16.619 6.888s12.183-2.446 16.616-6.884l95.849-95.846c.454-.442.891-.909 1.308-1.396a23.39 23.39 0 0 0 5.533-12.347 23.72 23.72 0 0 0 .135-4.705z"
                />
                <path
                  d="M63.5 360h-40c-4.687 0-8.5-3.813-8.5-8.5s3.813-8.5 8.5-8.5h343.997a7.5 7.5 0 0 0 0-15H23.5C10.542 328 0 338.542 0 351.5S10.542 375 23.5 375h40a7.5 7.5 0 0 0 0-15z"
                />
              </svg>
              <h1
                id="fahrenheit_temp"
                class="text-3xl font-bold text-center mt-4 text-yellow-500 cursor-pointer"
              >
                {{ isFahrenheit }} &#8457;
              </h1>
            </div>
          </div>
        </div>
        <div class="bg-slate-100 p-3 rounded-lg">
          <h1 class="text-lg font-bold opacity-40">Extra Weather Details</h1>
          <div class="flex items-center justify-between mt-2">
            <div class="flex flex-col items-center">
              <p class="opacity-40 font-bold mt-2">Precipitation</p>
              <div class="w-14 mt-2">
                <img class="rounded-full" src="./assets/images/rain.png" alt="" />
              </div>
              <h1 id="precipitation" class="text-base font-bold mt-2">
                {{ searchedCityData.current.precip_mm }}mm
              </h1>
            </div>

            <div class="flex flex-col items-center">
              <p class="opacity-40 font-bold mt-2">Humidity</p>
              <div class="w-14 mt-2">
                <img class="rounded-full" src="./assets/images/humidity.png" alt="" />
              </div>
              <h1 id="humidity" class="text-base font-bold mt-2">
                {{ searchedCityData.current.humidity }}%
              </h1>
            </div>

            <div class="flex flex-col items-center">
              <p class="opacity-40 font-bold mt-2">Wind Speed</p>
              <div class="w-14 mt-2">
                <img class="rounded-full" src="./assets/images/wind.png" alt="" />
              </div>
              <h1 id="wind-speed" class="text-base font-bold mt-2">
                {{ searchedCityData.current.wind_mph }}km/h
              </h1>
            </div>
          </div>
        </div>
      </main>

      <div v-if="!showSearchedCities" class="Popular_cities">
        <div class="flex items-center justify-between">
          <p class="font-light text-2xl mt-8">Popular Cities</p>
          <button
            @click="viewCurrentCityWeather()"
            class="font-light text-lg mt-8"
            id="view_weather"
          >
            View Weather based on your current location
          </button>
        </div>

        <div
          v-for="(city, index) in fetchedData"
          :key="index"
          class="flex p-3 px-6 my-6 bg-slate-200 rounded-full items-center justify-between"
        >
          <div class="flex items-center gap-3">
            <div class="w-16">
              <img
                id="WeatherIcon"
                class="object-cover rounded-full"
                :src="city.current.condition.icon"
                alt=""
              />
            </div>
            <div>
              <h2 class="text-2xl font-semibold cityName">{{ city.location.name }}</h2>
              <p class="Time font-medium text-blue-300">{{ city.location.localtime }}</p>
              <p id="condition" class="font-sm text-yellow-500">
                {{ city.current.condition.text }}
              </p>
            </div>
          </div>
          <div class="flex flex-col items-center">
            <h1 id="Temp" class="text-2xl font-bold text-yellow-500">
              {{ Math.round(city.current.temp_c) }}<span id="degreeCelcius">&#8451;</span>
            </h1>
            <button
              @click="viewPopularCities(city)"
              id="city-container"
              class="text-xs mt-1 text-blue-400 font-semibold"
            >
              View Details
            </button>
          </div>
        </div>
      </div>

      <div id="Popular_cities2" v-if="showSearchedCities">
        <div>
          <p class="font-light text-xl mt-8">Popular Cities</p>
        </div>

        <div class="flex items-center justify-between flex-wrap">
          <div v-for="(city, index) in fetchedData" :key="index" class="p-3 px-6 mt-2">
            <div class="flex items-center gap-3">
              <div class="w-9">
                <img id="pop_city2_WeatherIcon" class="object-cover rounded-full" src="" alt="" />
              </div>
              <div>
                <h2 class="text-lg font-semibold pop_cities2-city-name">
                  {{ city.location.name }}
                </h2>
                <p id="popCity_time" class="font-medium opacity-40">
                  {{ city.location.localtime }}
                </p>
                <h1 id="pop_city_temp" class="text-lg font-semibold mt-2">
                  {{ Math.round(city.current.temp_c) }} <span id="degreeCelcius">&#8451;</span>
                </h1>
                <p class="font-sm pop_cities2-city-condition">{{ city.current.condition.text }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>
  <!-- <div v-if="loader" class="loader" id="loader"></div> -->
</template>

<style scoped>
.inputErr {
  border: 1px solid red;
}
</style>
