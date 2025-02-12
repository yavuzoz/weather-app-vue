<script setup>
import { ref, onMounted, computed } from "vue";
import { capitalizeFirstLetter } from "./utils";
import HighLights from "./components/HighLights.vue";
// import HighLights from "./components/HighLights.vue";
import WeatherSummary from "./components/WeatherSummary.vue";
import Coords from "./components/Coords.vue";
import Humidity from "./components/Humidity.vue";

const API_KEY = import.meta.env.VITE_API_KEY;
const BASE_URL = import.meta.env.VITE_BASE_URL;

const showPopup = ref(true); // true, 


const city = ref('Bern');
const weatherInfo = ref(null);
const isError = computed(() => weatherInfo.value?.cod !== 200);

function getWeather() {
  fetch(`${BASE_URL}?q=${city.value}&units=metric&appid=${API_KEY}`)
    .then((response) => {
      console.log(response);
      // console.log(response.json());
       return response.json();
    }).then((data) => {
      console.log(data);
      weatherInfo.value = data;
    })
}

// onMounted(getWeather);

onMounted(() => {
  getWeather();
  setTimeout(() => {
    showPopup.value = false;
  }, 7000); 
});

</script>

<template>
  <div class="page">
    <div v-if="showPopup" class="popup">
      <div class="popup-content">
        <h2>Welcome weather app!</h2>
        <p>Enter the city name in the search field and press Enter</p>
      </div>
    </div>
    <!--  -->
      <main class="main">
        <div class="container">
          <div class="laptop">
            <div class="sections">
              <section class="section section-left">
                <div class="info">
                  {{ city }}
                  <div class="city-inner" @click="getWeather">
                    <input v-model="city" type="text" class="search" @keyup.enter="getWeather">
                  </div>
                  <WeatherSummary v-if="!isError" :weatherInfo="weatherInfo" />
                  <!--  -->
                  <div v-else class="error">
                    <div class="error-title">Oooops! Something went wrong!</div>
                    <div v-if="weatherInfo?.message" class="error-message">
                      {{ capitalizeFirstLetter(weatherInfo?.message) }}
                    </div>
                  </div>
                </div>
              </section>
              <section v-if="!isError" class="section section-right">
                <HighLights :weatherInfo="weatherInfo"/>
              </section> 
            </div>
            <div v-if="!isError" class="sections">
              <Coords :coord="weatherInfo.coord" />
              <Humidity :humidity="weatherInfo.main.humidity" />
            </div>
          </div>
        </div>
      </main>
  </div>
</template>

<style lang="sass" scoped>

</style>
<style lang="sass" scoped>
@import './assets/styles/main'

.page
  position: relative
  display: flex
  justify-content: center
  align-items: center
  min-height: 100vh
  padding: 20px 0
  background-color: #59585d

.laptop
  width: 100%
  padding: 20px
  background-color: #0e100f
  border-radius: 25px

.sections
  display: flex
  width: 100%

  @media (max-width: 767px)
    flex-direction: column

.section-left
  width: 30%
  padding-right: 10px
  padding-bottom: 10px

  @media (max-width: 767px)
    width: 100%
    padding-right: 0

.section-right
  width: 70%
  padding-left: 10px
  padding-bottom: 10px

  @media (max-width: 767px)
    width: 100%
    margin-top: 16px
    padding-left: 0

.city-inner
  position: relative
  display: inline-block
  width: 100%
  cursor: pointer

  &::after
    content: ''
    position: absolute
    top: 0
    right: 10px
    width: 25px
    height: 25px
    background: url('./assets/img/search.svg') no-repeat 50% 50%
    background-size: contain
    transform: translateY(50%)
    cursor: pointer

.info
  height: 100%
  padding: 16px
  background: url('./assets/img/gradient-1.jpg') no-repeat 50% 50%
  background-size: cover
  border-radius: 25px

.search
  width: 100%
  padding: 16px
  font-family: 'Inter', Arial, sans-serif
  color: $white
  background-color: rgba(0, 0, 0, 0.75)
  border-radius: 16px
  border: none
  outline: none
  cursor: pointer

.section-bottom
  width: 50%
  margin-top: 16px

  @media (max-width: 767px)
    width: 100%

.error
  padding-top: 20px

  &-title
    font-size: 18px
    font-weight: 700

  &-message
    padding-top: 10px
    font-size: 12px
//Popup window
.popup
  position: fixed
  top: 0
  left: 0
  width: 100%
  height: 100%
  background: rgba(0, 0, 0, 0.5)
  display: flex
  justify-content: center
  align-items: center
  z-index: 1000

.popup-content
  background: #b6bad8
  padding: 20px
  border-radius: 8px
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.3)
  text-align: center
  color: #ac2a2a
  max-width: 300px
  width: clamp(300px, 50%, 800px)
//
.btn-76
  border: 0 solid
  box-sizing: border-box
  -webkit-tap-highlight-color: transparent
  -webkit-appearance: button
  background-color: #000
  background-image: none
  color: #fff
  cursor: pointer
  font-family: ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Helvetica Neue, Arial, Noto Sans, sans-serif, Apple Color Emoji, Segoe UI Emoji, Segoe UI Symbol, Noto Color Emoji
  font-size: 100%
  line-height: 0.5
  margin: 0
  -webkit-mask-image: -webkit-radial-gradient(#000, #fff)
  padding: 0
  margin: 0 auto

  --neon: #0ea5e9
  box-sizing: border-box
  display: block
  font-weight: 900
  -webkit-mask-image: none
  outline: 4px solid #fff
  outline-offset: -4px
  overflow: hidden
  padding: 1.2rem 3rem
  position: relative
  text-transform: uppercase
  transition: 0.2s linear 0.1s

  *, :after, :before, &:after, &:before
    border: 0 solid
    box-sizing: border-box

  &:disabled
    cursor: default

  &:-moz-focusring
    outline: auto

  svg
    display: block
    vertical-align: middle

  [hidden]
    display: none

  &:hover
    background: var(--neon)
    box-shadow: 0 0 5px var(--neon), 0 0 25px var(--neon), 0 0 50px var(--neon), 0 0 100px var(--neon)
    color: #fff
    outline-color: transparent
    transition: 0.2s linear 0.6s

  span
    display: block
    inset: 0
    position: absolute

  .top
    border-top: 4px solid var(--neon)
    opacity: 0
    transform: translateX(calc(-100% + var(--progress, 0%)))
    transition: none

  &:hover .top
    --progress: 100%
    opacity: 1
    transition: transform 0.2s linear

  .right
    border-right: 4px solid var(--neon)
    opacity: 0
    transform: translateY(calc(-100% + var(--progress, 0%)))
    transition: none

  &:hover .right
    --progress: 100%
    opacity: 1
    transition: transform 0.2s linear 0.2s

  .bottom
    border-bottom: 4px solid var(--neon)
    opacity: 0
    transform: translateX(calc(100% - var(--progress, 0%)))
    transition: none

  &:hover .bottom
    --progress: 100%
    opacity: 1
    transition: transform 0.2s linear 0.4s

  .left
    border-left: 4px solid var(--neon)
    opacity: 0
    transform: translateY(calc(100% - var(--progress, 0%)))
    transition: none

  &:hover .left
    --progress: 100%
    opacity: 1
    transition: transform 0.2s linear 0.6s

</style>
