<script setup>
import { ref, onMounted, watch } from 'vue'
import Game from './components/Game.vue'
import Loading from './components/Loading.vue'
import axios from 'axios'

const places = ref([])
const id = ref(0)
const loading = ref(true)

const audioPlayer = ref(null)

const generateRandomCoordinates = () => {
  const latitude = (Math.random() * 180 - 90).toFixed(5)

  console.log(latitude)
  const longitude = (Math.random() * 360 - 180).toFixed(5)
  console.log(longitude)
  return { latitude, longitude }
}

const fetchPlaces = async () => {
  const { latitude, longitude } = generateRandomCoordinates() // Генерация новых координат перед запросом
  try {
    if (loading.value === false) loading.value = true

    const { data } = await axios.get(
      `https://pastvu.com/api2?method=photo.giveNearestPhotos&params={"geo":[${latitude},${longitude}],"limit":1}`
    )

    places.value = data.result.photos.map((obj) => ({
      ...obj,
      id: id.value++
    }))
  } catch (error) {
    console.log(error)
  }
}

onMounted(async () => {
  await fetchPlaces()
})
onMounted(() => {
  try {
    // Попытка автоматически воспроизвести музыку при загрузке
    audioPlayer.value.play().catch((e) => {
      console.error('Автовоспроизведение не удалось', e)
      // Здесь можно добавить логику для взаимодействия с пользователем, например кнопку для воспроизведения музыки
    })
  } catch (e) {
    console.error('Ошибка воспроизведения', e)
  }
})

watch(places, async () => {
  if (places.value.length == 0) {
    await fetchPlaces()
  } else {
    loading.value = false
  }
  // console.log(places.value.length)
})
</script>

<template>
  <div class="flex h-dvh w-dvw items-center justify-center">
    <audio ref="audioPlayer" loop>
      <source src="/slavik.mp3" type="audio/mp3" />
      Ваш браузер не поддерживает аудио элемент.
    </audio>
    <Loading v-if="loading" />
    <Game
      v-else
      v-for="place in places"
      :key="place.cid"
      :title="place.title"
      :image="place.file"
      :year-place="place.year"
      @fetchPlaces="fetchPlaces"
      :loading="loading"
    />
  </div>
</template>

<style>
.btn {
  @apply text-2xl rounded-md bg-yellow-400 px-16 py-4 transition hover:bg-blue-500 hover:text-gray-50;
}
</style>
