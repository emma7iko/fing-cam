<template>
  <div class="pie-chart">
    <svg :width="size" :height="size" viewBox="0 0 120 120">
      <g v-for="(v, i) in data" :key="i">
        <circle
          :r="radius"
          cx="60"
          cy="60"
          :stroke="colors[i]"
          stroke-width="18"
          fill="none"
          :stroke-dasharray="circ * tweened[i] + ' ' + circ * (1-tweened[i])"
          :stroke-dashoffset="offsets[i]"
        />
      </g>
    </svg>
    <div class="label">Finger Pie Chart</div>
  </div>
</template>

<script setup>
const props = defineProps({
  data: { type: Array, default: () => [0, 0, 0, 0, 0] },
});
import { ref, watch } from 'vue';
const size = 120;
const radius = 42;
const circ = 2 * Math.PI * radius;
const colors = ['#42b883', '#ffb300', '#e57373', '#64b5f6', '#9575cd'];
const offsets = [0, -circ*0.2, -circ*0.4, -circ*0.6, -circ*0.8];

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
</script>

<style scoped>
.pie-chart {
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
