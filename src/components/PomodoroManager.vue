<template>
  <div class="space-y-6">
    <div class="flex gap-2">
      <input
        v-model="description"
        type="text"
        placeholder="Descripción"
        class="bg-zinc-700 text-white px-3 py-2 rounded-xl w-1/2"
      />
      <input
        v-model.number="minutes"
        type="number"
        placeholder="Min"
        class="bg-zinc-700 text-white px-3 py-2 rounded-xl w-1/4"
        min="1"
      />
      <button
        @click="addTimer"
        class="bg-green-600 hover:bg-green-700 px-4 py-2 rounded-xl text-white"
      >
        ➕
      </button>
    </div>

    <div v-if="timers.length === 0" class="text-zinc-400">
      No hay temporizadores creados.
    </div>

    <div class="grid md:grid-cols-2 xl:grid-cols-3 gap-4">
      <PomodoroTimer
        v-for="timer in timers"
        :key="timer.id"
        :id="timer.id"
        :description="timer.description"
        :duration="timer.duration"
        @remove="removeTimer"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'
import PomodoroTimer from './PomodoroTimer.vue'

const timers = ref([])
const description = ref('')
const minutes = ref(25) // Por defecto 25 minutos

// 🗂️ Cargar timers desde localStorage al iniciar
onMounted(() => {
  const saved = localStorage.getItem('timers')
  if (saved) {
    timers.value = JSON.parse(saved)
  }
})

// 💾 Guardar en localStorage cada vez que cambia
watch(
  timers,
  (newVal) => {
    localStorage.setItem('timers', JSON.stringify(newVal))
  },
  { deep: true }
)

const addTimer = () => {
  if (!description.value || !minutes.value) return

  timers.value.push({
    id: Date.now(),
    description: description.value,
    duration: minutes.value * 60, // Convertimos minutos a segundos
  })

  description.value = ''
  minutes.value = 25
}

const removeTimer = (id) => {
  timers.value = timers.value.filter((t) => t.id !== id)
}
</script>
