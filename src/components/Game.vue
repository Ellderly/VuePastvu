<script setup>
import { ref, onMounted } from 'vue'

const props = defineProps({
  title: String,
  image: String,
  yearPlace: Number,
  loading: Boolean
})

const emit = defineEmits(['fetchPlaces'])
const year = ref(1800)

let failAudio

onMounted(() => {
  failAudio = new Audio('/slavik.mp3')
})

const btnClickYear = () => {
  if (Number(year.value) === Number(props.yearPlace)) {
    alert('Ты угадал с датой')
    failAudio.play()
  } else {
    alert(`Ты не угадал, ты лох, это ${props.yearPlace} год`)
    failAudio.play()
  }
}
</script>

<template>
  <div class="w-full flex flex-col items-center">
    <img :src="'https://pastvu.com/_p/d/' + image" alt="" class="w-1/2" />
    <h2>Название: {{ title }}</h2>

    <input
      class="w-3/4 h-2 bg-gray-200 rounded-lg appearance-none cursor-pointer dark:bg-gray-700"
      type="range"
      id="volume"
      name="volume"
      min="1800"
      max="2000"
      v-model="year"
    />
    <p>Год: {{ year }}</p>
    <button @click="btnClickYear" type="button" class="btn mt-6">Отправить</button>
    <button @click="() => emit('fetchPlaces')" type="button" class="btn mt-6">Перезапустить</button>
  </div>
</template>

<style></style>
