<template>
  <div class="camera-finger-app">
    <div class="camera-section">
      <video ref="videoRef" autoplay playsinline class="camera-video"></video>
      <canvas ref="canvasRef" class="camera-canvas"></canvas>
    </div>
    <div class="bar-graph-section">
      <BarGraph :bars="bars" />
    </div>
    <div class="logs-section">
      <h3>Logs</h3>
      <div class="logs-container">
        <div v-for="(log, idx) in logs" :key="idx" class="log-entry">{{ log }}</div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, reactive } from 'vue'
import { Hands } from '@mediapipe/hands'
import { Camera } from '@mediapipe/camera_utils'
import BarGraph from './BarGraph.vue'

const videoRef = ref(null)
const canvasRef = ref(null)
const bars = reactive([
  { name: 'Finger 1', value: 0 },
  { name: 'Finger 2', value: 0 },
  { name: 'Finger 3', value: 0 },
  { name: 'Finger 4', value: 0 },
  { name: 'Finger 5', value: 0 },
])
const logs = ref([])

function log(msg) {
  logs.value.push(`[${new Date().toLocaleTimeString()}] ${msg}`)
  if (logs.value.length > 100) logs.value.shift()
  // For terminal logging: send to backend if available (future step)
  if (window.__logToTerminal) window.__logToTerminal(msg)
}

function animateBar(idx, to) {
  const step = to > bars[idx].value ? 0.05 : -0.05
  function animate() {
    if ((step > 0 && bars[idx].value < to) || (step < 0 && bars[idx].value > to)) {
      bars[idx].value = Math.max(0, Math.min(1, bars[idx].value + step))
      requestAnimationFrame(animate)
    } else {
      bars[idx].value = to
    }
  }
  animate()
}

function updateBars(fingers) {
  for (let i = 0; i < 5; i++) {
    animateBar(i, fingers[i] ? 1 : 0)
    log(`${bars[i].name} is ${fingers[i] ? 'raised' : 'down'}`)
  }
}

function getFingersUp(landmarks) {
  // Thumb: landmarks[4] (tip), [3] (knuckle), [2] (base)
  // Index: [8] (tip), [6] (base)
  // Middle: [12] (tip), [10] (base)
  // Ring: [16] (tip), [14] (base)
  // Pinky: [20] (tip), [18] (base)
  if (!landmarks) return [0, 0, 0, 0, 0]
  const fingers = []
  // Thumb: check if tip is to the right of base (for right hand)
  fingers[0] = landmarks[4].x < landmarks[3].x ? 1 : 0
  // Other fingers: tip is above base
  fingers[1] = landmarks[8].y < landmarks[6].y ? 1 : 0
  fingers[2] = landmarks[12].y < landmarks[10].y ? 1 : 0
  fingers[3] = landmarks[16].y < landmarks[14].y ? 1 : 0
  fingers[4] = landmarks[20].y < landmarks[18].y ? 1 : 0
  return fingers
}

onMounted(() => {
  const hands = new Hands({
    locateFile: (file) => `https://cdn.jsdelivr.net/npm/@mediapipe/hands/${file}`
  })
  hands.setOptions({
    maxNumHands: 1,
    modelComplexity: 1,
    minDetectionConfidence: 0.7,
    minTrackingConfidence: 0.7
  })
  hands.onResults((results) => {
    const ctx = canvasRef.value.getContext('2d')
    ctx.clearRect(0, 0, canvasRef.value.width, canvasRef.value.height)
    if (results.multiHandLandmarks && results.multiHandLandmarks.length > 0) {
      const landmarks = results.multiHandLandmarks[0]
      updateBars(getFingersUp(landmarks))
      // Draw landmarks
      for (const lm of landmarks) {
        ctx.beginPath()
        ctx.arc(lm.x * canvasRef.value.width, lm.y * canvasRef.value.height, 5, 0, 2 * Math.PI)
        ctx.fillStyle = 'aqua'
        ctx.fill()
      }
    } else {
      updateBars([0, 0, 0, 0, 0])
    }
  })

  navigator.mediaDevices.getUserMedia({ video: true }).then((stream) => {
    videoRef.value.srcObject = stream
    videoRef.value.play()
    log('Camera access granted')
    canvasRef.value.width = 480
    canvasRef.value.height = 360
    const camera = new Camera(videoRef.value, {
      onFrame: async () => {
        await hands.send({ image: videoRef.value })
      },
      width: 480,
      height: 360,
    })
    camera.start()
  }).catch(() => {
    log('Camera access denied')
  })
})
</script>

<style scoped>
.camera-finger-app {
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 100vh;
  background: #f7fafd;
  padding: 32px 0 0 0;
}
.camera-section {
  position: relative;
  width: 480px;
  height: 360px;
  margin-bottom: 32px;
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: 0 6px 24px #0002;
  border-radius: 14px;
  background: #fff;
}
.camera-video {
  width: 480px;
  height: 360px;
  border-radius: 14px;
  box-shadow: none;
}
.camera-canvas {
  position: absolute;
  left: 0;
  top: 0;
  width: 480px;
  height: 360px;
  pointer-events: none;
}
.bar-graph-section {
  width: 480px;
  margin: 0 auto 32px auto;
  background: #f1f5ff;
  border-radius: 12px;
  box-shadow: 0 2px 8px #0001;
  padding: 18px 0 18px 0;
  display: flex;
  justify-content: center;
}
.logs-section {
  width: 90vw;
  max-width: 600px;
  margin: 0 auto 0 auto;
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 2px 8px #0001;
  padding: 16px;
  margin-bottom: 32px;
}
.logs-section h3 {
  margin: 0 0 10px 0;
  font-weight: 600;
  font-size: 1.1em;
  color: #2563eb;
}
.logs-container {
  max-height: 120px;
  overflow-y: auto;
  font-family: monospace;
  font-size: 0.97em;
  background: #f6f8fa;
  border-radius: 6px;
  padding: 6px 8px;
}
.log-entry {
  padding: 2px 0;
  border-bottom: 1px solid #f0f0f0;
}
</style>
