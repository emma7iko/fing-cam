<template>
  <div class="radar-chart">
    <svg :width="size" :height="size" viewBox="0 0 120 120">
      <polygon :points="polygonPoints" fill="#42b88333" stroke="#42b883" stroke-width="2" />
      <g v-for="(angle, i) in angles" :key="i">
        <line :x1="center" :y1="center" :x2="center + Math.sin(angle) * radius" :y2="center - Math.cos(angle) * radius" stroke="#bbb" />
        <circle :cx="center + Math.sin(angle) * radius * tweened[i]" :cy="center - Math.cos(angle) * radius * tweened[i]" r="5" fill="#42b883" />
      </g>
    </svg>
    <div class="label">Finger Radar Chart</div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue';
const props = defineProps({
  data: { type: Array, default: () => [0, 0, 0, 0, 0] },
});
const size = 120;
const center = 60;
const radius = 48;
const angles = [0, (2*Math.PI)/5, (4*Math.PI)/5, (6*Math.PI)/5, (8*Math.PI)/5];
const tweened = ref([...props.data]);
watch(() => props.data.slice(), (newData) => {
  newData.forEach((v, i) => {
    const start = tweened.value[i];
    const end = v;
    const duration = 350;
    const startTime = performance.now();
    function animate(now) {
      const t = Math.min((now - startTime) / duration, 1);
      tweened.value[i] = start + (end - start) * t;
      if (t < 1) requestAnimationFrame(animate);
    }
    requestAnimationFrame(animate);
  });
}, { deep: true });
const polygonPoints = computed(() =>
  tweened.value.map((v, i) => {
    const x = center + Math.sin(angles[i]) * radius * v;
    const y = center - Math.cos(angles[i]) * radius * v;
    return `${x},${y}`;
  }).join(' ')
);
</script>

<style scoped>
.radar-chart {
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 2px 8px #0001;
  padding: 1rem;
  margin: 0.5rem;
  width: 140px;
  max-width: 98vw;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.label {
  margin-top: 0.5rem;
  font-size: 1rem;
  color: #333;
}
</style>
