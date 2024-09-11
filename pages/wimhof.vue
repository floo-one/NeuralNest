<script setup>
import { computed, onMounted, ref } from 'vue'

// Constants
const CYCLES = 3
const BREATHS = 60
const INHALE_DURATION = 2
const EXHALE_DURATION = 2
const INITIAL_REST_DURATION = 15

// Reactive state
const sessionStarted = ref(false)
const sessionInProgress = ref(false)
const sessionCompleted = ref(false)
const currentCycle = ref(1)
const currentBreath = ref(1)
const breathingPhase = ref(true)
const holdingPhase = ref(false)
const restingPhase = ref(false)
const holdDuration = ref(0)
const sessionResults = ref([])
const breathingInstruction = ref('')
const restDuration = ref(INITIAL_REST_DURATION)

// Timer references
const breathTimer = ref(null)
const holdTimer = ref(null)
const restTimer = ref(null)

// Audio context and sounds
const audioContext = ref(null)
const sounds = ref({
  inhale: null,
  exhale: null,
  hold: null,
  rest: null,
})

// New method to load and play sounds
function playSound(soundName) {
  if (sounds.value[soundName]) {
    const source = audioContext.value.createBufferSource()
    source.buffer = sounds.value[soundName]
    source.connect(audioContext.value.destination)
    source.start()
  }
}

// Load audio files
onMounted(async () => {
  audioContext.value = new (window.AudioContext || window.webkitAudioContext)()

  const soundFiles = {
    inhale: '/sounds/inhale.mp3',
    exhale: '/sounds/exhale.mp3',
    hold: '/sounds/hold.mp3',
    rest: '/sounds/rest.mp3',
  }

  for (const [name, file] of Object.entries(soundFiles)) {
    const response = await fetch(file)
    const arrayBuffer = await response.arrayBuffer()
    const audioBuffer = await audioContext.value.decodeAudioData(arrayBuffer)
    sounds.value[name] = audioBuffer
  }
})

// Computed properties
const isInhaling = computed(() => currentBreath.value % 2 !== 0)

// Methods
function startSession() {
  sessionStarted.value = true
  sessionInProgress.value = true
  runCycle()
}

async function runCycle() {
  // Breathing phase
  for (let i = 1; i <= BREATHS; i++) {
    currentBreath.value = i
    await breath()
  }

  // Hold phase
  breathingPhase.value = false
  holdingPhase.value = true
  holdDuration.value = 0
  startHoldTimer()
}

function breath() {
  return new Promise((resolve) => {
    breathingInstruction.value = isInhaling.value ? 'Inhale' : 'Exhale'
    playSound(isInhaling.value ? 'inhale' : 'exhale')
    breathTimer.value = setTimeout(resolve, (isInhaling.value ? INHALE_DURATION : EXHALE_DURATION) * 1000)
  })
}

function startHoldTimer() {
  playSound('hold')
  holdTimer.value = setInterval(() => {
    holdDuration.value++
  }, 1000)
}

function endHold() {
  clearInterval(holdTimer.value)
  holdingPhase.value = false
  restingPhase.value = true
  restDuration.value = INITIAL_REST_DURATION

  sessionResults.value.push({
    cycle_number: currentCycle.value,
    hold_duration: holdDuration.value,
  })

  startRestTimer()
}

function startRestTimer() {
  playSound('rest')
  restTimer.value = setInterval(() => {
    if (restDuration.value > 0) {
      restDuration.value--
    }
    else {
      clearInterval(restTimer.value)
      restingPhase.value = false
      if (currentCycle.value < CYCLES) {
        currentCycle.value++
        breathingPhase.value = true
        runCycle()
      }
      else {
        completeSession()
      }
    }
  }, 1000)
}

function completeSession() {
  sessionInProgress.value = false
  sessionCompleted.value = true
}

// Cleanup function
onUnmounted(() => {
  clearTimeout(breathTimer.value)
  clearInterval(holdTimer.value)
  clearInterval(restTimer.value)
})
</script>

<template>
  <div class="h-full flex flex-col p-6 font-sans">
    <h1 class="text-5xl font-bold mb-10 text-center">
      Wim Hof Breathing Method
    </h1>
    <div v-if="!sessionStarted" class="flex-grow flex items-center justify-center">
      <button :disabled="sessionInProgress" class="text-2xl font-bold py-4 px-8 rounded disabled:opacity-50" @click="startSession">
        Start Session
      </button>
    </div>
    <div v-else class="flex-grow flex flex-col justify-center space-y-8">
      <h2 class="text-4xl font-semibold text-center">
        Cycle {{ currentCycle }} of {{ CYCLES }}
      </h2>
      <div v-if="breathingPhase" class="text-center">
        <p class="text-3xl mb-4">
          Breath {{ currentBreath }} of {{ BREATHS }}
        </p>
        <p class="text-6xl font-bold">
          {{ breathingInstruction }}
        </p>
      </div>
      <div v-else-if="holdingPhase" class="text-center">
        <p class="text-3xl mb-4">
          Hold your breath
        </p>
        <p class="text-6xl font-bold mb-8">
          Time: {{ holdDuration }}s
        </p>
        <button class="text-2xl font-bold py-4 px-8 rounded" @click="endHold">
          End Hold
        </button>
      </div>
      <div v-else-if="restingPhase" class="text-center">
        <p class="text-4xl">
          Rest for {{ restDuration }} seconds
        </p>
      </div>
    </div>
    <div v-if="sessionCompleted" class="mt-12">
      <h2 class="text-4xl font-semibold mb-6 text-center">
        Session Results
      </h2>
      <ul class="space-y-4">
        <li v-for="result in sessionResults" :key="result.cycle_number" class="text-2xl p-4 rounded">
          Cycle {{ result.cycle_number }}: Hold duration {{ result.hold_duration }}s
        </li>
      </ul>
    </div>
  </div>
</template>
