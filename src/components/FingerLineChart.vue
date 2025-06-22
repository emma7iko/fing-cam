<template>
  <div class="line-chart">
    <svg :width="width" :height="height">
      <polyline :points="points" fill="none" stroke="#42b883" stroke-width="4" />
      <circle v-for="(y, i) in data" :key="i" :cx="xStep * i + xStep/2" :cy="height - y * (height-20)" r="6" fill="#42b883" />
    </svg>
    <div class="label">Finger Line Chart</div>
  </div>
</template>

<script setup>
import { computed } from 'vue';
const props = defineProps({
  data: { type: Array, default: () => [0, 0, 0, 0, 0] },
});
import { ref, watch } from 'vue';
const width = 300;
const height = 100;
const xStep = width / props.data.length;
const tweenedData = ref([...props.data]);

watch(() => props.data.slice(), (newData) => {
  newData.forEach((v, i) => {
    const start = tweenedData.value[i];
    const end = v;
    const duration = 350;
    const startTime = performance.now();
    function animate(now) {
      const t = Math.min((now - startTime) / duration, 1);
      tweenedData.value[i] = start + (end - start) * t;
      if (t < 1) requestAnimationFrame(animate);
    }
    requestAnimationFrame(animate);
  });
}, { deep: true });

const points = computed(() =>
  tweenedData.value.map((y, i) => `${xStep * i + xStep/2},${height - y * (height-20)}`).join(' ')
);
</script>

<style scoped>
.line-chart {
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 2px 8px #0001;
  padding: 1rem;
  margin: 0.5rem;
  width: 320px;
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
