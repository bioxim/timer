<template>
  <div class="bg-zinc-800 p-4 rounded-2xl shadow-lg w-full max-w-sm">
    <h2 class="text-xl font-bold text-white mb-2">{{ props.description }}</h2>
    <div class="flex items-center justify-center mb-4">
      <span class="text-5xl font-mono text-white">{{ minutes }}:{{ seconds }}</span>
    </div>
    <div class="flex justify-center gap-2">
      <button
        class="bg-green-600 hover:bg-green-700 px-4 py-2 rounded-xl text-white"
        @click="start"
      >
        ▶️
      </button>
      <button
        class="bg-yellow-500 hover:bg-yellow-600 px-4 py-2 rounded-xl text-white"
        @click="pause"
      >
        ⏸️
      </button>
      <button
        class="bg-blue-600 hover:bg-blue-700 px-4 py-2 rounded-xl text-white"
        @click="reset"
      >
        🔄
      </button>
      <button
        class="bg-red-600 hover:bg-red-700 px-4 py-2 rounded-xl text-white"
        @click="emit('remove', props.id)"
      >
        ❌
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onUnmounted } from 'vue'

const props = defineProps({
  id: Number,
  description: String,
  duration: Number,
})

const emit = defineEmits(['remove'])

const timeLeft = ref(props.duration)
const isRunning = ref(false)
let interval = null

const alarm = new Audio('/sound.wav')
alarm.volume = 1

const notify = () => {
  if (Notification.permission === 'granted') {
    new Notification(`✅ "${props.description}" ha terminado.`)
  } else if (Notification.permission !== 'denied') {
    Notification.requestPermission().then(permission => {
      if (permission === 'granted') {
        new Notification(`✅ "${props.description}" ha terminado.`)
      }
    })
  }
}

const minutes = computed(() => Math.floor(timeLeft.value / 60).toString().padStart(2, '0'))
const seconds = computed(() => (timeLeft.value % 60).toString().padStart(2, '0'))

const start = () => {
  if (isRunning.value) return
  isRunning.value = true
  interval = setInterval(() => {
    if (timeLeft.value > 0) {
      timeLeft.value--
    } else {
      pause()
      alarm.currentTime = 0
      alarm.play()
      notify()
    }
  }, 1000)
}

const pause = () => {
  isRunning.value = false
  if (interval) clearInterval(interval)
}

const reset = () => {
  pause()
  timeLeft.value = props.duration
}

onUnmounted(() => {
  if (interval) clearInterval(interval)
})
</script>
