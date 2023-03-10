<script setup lang="ts">
import { computed, onMounted, watch } from 'vue'
import { weatherStore } from '../store'

export interface Props {
  apikey?: string
  lat: string
  lon: string
  imperial?: boolean
}

const props = defineProps<Props>()
const {
  weatherData,
  weatherIconURL,
  fetchWeatherData,
  currentTemperature,
} = weatherStore()

const setWeatherData = async () => {
  if (!props.apikey)
    return
  await fetchWeatherData(props)
  weatherIconURL.value = `http://openweathermap.org/img/wn/${weatherData.value?.current.weather[0].icon}@4x.png`
}

onMounted(async () => {
  await setWeatherData()
})

watch(() => [props.apikey, props.imperial], async () => {
  await setWeatherData()
})

const unitType = computed(() => {
  return props.imperial ? 'F' : 'C'
})
</script>

<template>
  <div class="weather-container">
    <div class="weather-information">
      <img v-if="weatherIconURL" class="weather-information-icon" :src="weatherIconURL" alt="Weather icon">
      <p class="weather-temperature">
        {{ currentTemperature }}°{{ unitType }}
      </p>
    </div>
  </div>
</template>

<style scoped>
.weather-container {
    padding: 1em 1.5em;
    background-color: hsl(0, 10%, 98%);
    width: 8em;
    height: 8em;
    display: flex;
    flex-direction: column;
    justify-content: center;
    position: relative;
    border-radius: 15px;
    box-shadow: rgba(99, 99, 99, 0.2) 5px 2px 8px 0px;
}

.weather-information {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
}

.weather-information-icon {
  width: 100px;
  height: 100px;
}

.weather-temperature {
    font-size: 1.7em;
    font-weight: 500;
}
</style>
