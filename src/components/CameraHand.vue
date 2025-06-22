<template>
  <div class="camera-section">
    <template v-if="!cameraError">
      <video ref="video" autoplay playsinline class="camera-video"></video>
      <canvas ref="canvas" class="skeleton-canvas"></canvas>
      <div class="camera-status">{{ cameraStatus }}</div>
    </template>
    <div v-else class="camera-error">
      <p>{{ cameraError }}</p>
      <div class="camera-status">{{ cameraStatus }}</div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { HandLandmarker, FilesetResolver } from '@mediapipe/tasks-vision';

const emits = defineEmits(['fingerStatus']);
const video = ref(null);
const canvas = ref(null);
const cameraError = ref('');
const cameraStatus = ref('Initializing...');
let handLandmarker = null;
let running = false;

const FINGER_INDICES = [4, 8, 12, 16, 20]; // Thumb, Index, Middle, Ring, Pinky

async function setupHandLandmarker() {
  cameraStatus.value = 'Loading hand tracker...';
  try {
    const vision = await FilesetResolver.forVisionTasks(
      'https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision@latest/wasm'
    );
    handLandmarker = await HandLandmarker.createFromOptions(vision, {
      baseOptions: {
        modelAssetPath:
          'https://storage.googleapis.com/mediapipe-assets/hand_landmarker.task',
        delegate: 'GPU',
      },
      runningMode: 'VIDEO',
      numHands: 1,
    });
    cameraStatus.value = 'Hand tracker loaded.';
    console.log('HandLandmarker loaded');
  } catch (err) {
    cameraError.value = 'Failed to load hand tracker.';
    cameraStatus.value = 'Failed to load hand tracker.';
    console.error('HandLandmarker error', err);
  }
}

function getFingerStatus(landmarks) {
  // Simple logic: if tip is above pip in y-axis (for most fingers)
  // For thumb, compare x-axis
  if (!landmarks) return [0, 0, 0, 0, 0];
  const status = [0, 0, 0, 0, 0];
  // Thumb
  status[0] = landmarks[4].x < landmarks[3].x ? 1 : 0;
  // Other fingers
  for (let i = 1; i < 5; i++) {
    status[i] = landmarks[FINGER_INDICES[i]].y < landmarks[FINGER_INDICES[i] - 2].y ? 1 : 0;
  }
  return status;
}


async function startCamera() {
  cameraStatus.value = 'Requesting camera access...';
  try {
    console.log('Requesting camera');
    const stream = await navigator.mediaDevices.getUserMedia({ video: true });
    cameraStatus.value = 'Camera access granted.';
    video.value.srcObject = stream;
    video.value.onloadedmetadata = () => {
      console.log('Video metadata loaded');
      video.value.play();
      canvas.value.width = video.value.videoWidth;
      canvas.value.height = video.value.videoHeight;
      running = true;
      cameraStatus.value = 'Camera started.';
      detectLoop();
    };
  } catch (err) {
    cameraError.value = 'Unable to access camera. Please check permissions and device availability.';
    cameraStatus.value = 'Camera access denied or unavailable.';
    emits('fingerStatus', [0, 0, 0, 0, 0]);
    console.error('Camera error', err);
  }
}

async function detectLoop() {
  if (!running || !handLandmarker) return;
  const ctx = canvas.value.getContext('2d');
  ctx.clearRect(0, 0, canvas.value.width, canvas.value.height);
  // Use video.value as HTMLVideoElement for detectForVideo
  const result = await handLandmarker.detectForVideo(video.value, performance.now());
  if (result.landmarks && result.landmarks.length > 0) {
    drawSkeleton(result.landmarks[0]);
    const status = getFingerStatus(result.landmarks[0]);
    emits('fingerStatus', status);
    console.log('Hand detected:', status, result.landmarks[0]);
  } else {
    emits('fingerStatus', [0, 0, 0, 0, 0]);
    // Optionally log no hand
  }
  requestAnimationFrame(detectLoop);
}

function drawSkeleton(landmarks) {
  const ctx = canvas.value.getContext('2d');
  ctx.clearRect(0, 0, canvas.value.width, canvas.value.height);
  if (!landmarks) return;
  ctx.save();
  ctx.strokeStyle = '#00FF00';
  ctx.lineWidth = 3;
  // Draw connections (MediaPipe hand connections)
  const connections = [
    [0,1],[1,2],[2,3],[3,4], // Thumb
    [0,5],[5,6],[6,7],[7,8], // Index
    [5,9],[9,10],[10,11],[11,12], // Middle
    [9,13],[13,14],[14,15],[15,16], // Ring
    [13,17],[17,18],[18,19],[19,20], // Pinky
    [0,17] // Palm base
  ];
  for (const [start, end] of connections) {
    ctx.beginPath();
    ctx.moveTo(landmarks[start].x * canvas.value.width, landmarks[start].y * canvas.value.height);
    ctx.lineTo(landmarks[end].x * canvas.value.width, landmarks[end].y * canvas.value.height);
    ctx.stroke();
  }
  ctx.restore();
  // Draw joints
  ctx.fillStyle = '#00FF00';
  for (const point of landmarks) {
    ctx.beginPath();
    ctx.arc(point.x * canvas.value.width, point.y * canvas.value.height, 6, 0, 2 * Math.PI);
    ctx.fill();
  }
}


onMounted(async () => {
  await setupHandLandmarker();
  await startCamera();
});

onBeforeUnmount(() => {
  running = false;
  if (video.value && video.value.srcObject) {
    video.value.srcObject.getTracks().forEach(track => track.stop());
  }
});
</script>

<style scoped>
.camera-section {
  position: relative;
  width: 100%;
  aspect-ratio: 4/3;
  background: #222;
  overflow: hidden;
  min-height: 240px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.camera-error {
  color: #fff;
  background: #c00;
  padding: 2rem;
  border-radius: 10px;
  text-align: center;
  width: 100%;
}
.camera-status {
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  color: #fff;
  background: rgba(0,0,0,0.6);
  text-align: center;
  font-size: 1rem;
  padding: 0.3rem 0;
  z-index: 10;
}

.camera-video {
  width: 100%;
  height: auto;
  display: block;
}
.skeleton-canvas {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}
</style>
