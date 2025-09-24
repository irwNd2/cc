<template>
  <div class="wrapper">
    <div class="content">
      <div class="info-left">
        <p class="date">{{ date }}</p>
        <h2 class="location">{{ weather?.name }}, {{ weather?.sys?.country }}</h2>
        <h1 class="temp">{{ tempC }}°C</h1>
        <p class="desc">
          Feels like {{ feelsC }}°C. 
          {{ weather?.weather[0]?.description }}. 
          {{ windDesc }}
        </p>
        <div class="details">
          <p>{{ weather?.wind?.speed }} m/s {{ windDir }}</p>
          <p>Pressure: {{ weather?.main?.pressure }} hPa</p>
          <p>Humidity: {{ weather?.main?.humidity }}%</p>
          <p>UV: 5 (mock)</p>
          <p>Dew point: {{ dewPoint }}°C</p>
          <p>Visibility: {{ (weather?.visibility/1000).toFixed(1) }} km</p>
        </div>
      </div>

      <div class="map">
        <iframe
          width="100%"
          height="100%"
          :src="`https://www.openstreetmap.org/export/embed.html?bbox=${lon-0.1},${lat-0.1},${lon+0.1},${lat+0.1}&layer=mapnik`"
          style="border:1px solid black">
        </iframe>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import axios from 'axios'

const weather = ref<any>(null)
const lat = ref<number>(0)
const lon = ref<number>(0)
const date = new Date().toLocaleString('id-ID', {
  weekday: 'short', day: 'numeric', month: 'short',
  hour: '2-digit', minute: '2-digit'
})

onMounted(async () => {
  const res = await axios.get(
    'https://api.openweathermap.org/data/2.5/weather?zip=10110&units=imperial&appid=a41d72c8d1ac8562a08038be387ac01e'
  )
  weather.value = res.data
  lat.value = res.data.coord.lat
  lon.value = res.data.coord.lon
})

const tempC = computed(() => weather.value?.main?.temp?.toFixed(0))
const feelsC = computed(() => weather.value?.main?.feels_like?.toFixed(0))
const dewPoint = computed(() => (weather.value?.main?.temp_min - 2).toFixed(0)) // mock aja
const windDir = computed(() => {
  const deg = weather.value?.wind?.deg
  if (deg >= 315 || deg < 45) return 'N'
  if (deg >= 45 && deg < 135) return 'E'
  if (deg >= 135 && deg < 225) return 'S'
  return 'W'
})
const windDesc = computed(() => {
  const speed = weather.value?.wind?.speed || 0
  if (speed < 1) return 'Calm'
  if (speed < 5) return 'Gentle Breeze'
  if (speed < 10) return 'Fresh Breeze'
  return 'Strong Wind'
})
</script>

<style scoped>
.wrapper {
  display: flex;
  flex-direction: column;
  max-width: 1280px;
  width: 100%;
  min-height: 100vh;
  padding: 16px;
}

.content {
  display: flex;
  justify-content: space-between;
  gap: 20px;
}

.info-left {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: left;
  max-width: 300px;
}

.date {
  color: #c00;
  font-size: 14px;
}

.location {
  font-size: 20px;
  font-weight: bold;
}

.temp {
  font-size: 40px;
  font-weight: bold;
}

.desc {
  font-size: 16px;
  color: #444;
  text-align: left;
}

.details {
  display: flex;
  gap: 4px;
  flex-wrap: nowrap;
  font-size: 14px;
}

.map {
  width: 400px;
  height: 250px;
}
</style>

