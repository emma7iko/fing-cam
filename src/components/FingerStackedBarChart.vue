<template>
  <div class="stacked-bar-chart">
    <div class="stacked-bar">
      <div
        v-for="(bar, idx) in bars"
        :key="idx"
        class="stacked-bar-segment"
        :style="{
          height: '100%',
          width: (tweened[idx] * 100) + '%',
          background: colors[idx],
          borderRight: idx < bars.length - 1 ? '2px solid #fff' : 'none',
          transition: 'width 0.45s cubic-bezier(0.23,1,0.32,1)'
        }"
      ></div>
    </div>
    <div class="label">Finger Stacked Bar Chart</div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue';
const props = defineProps({
  bars: { type: Array, default: () => [0, 0, 0, 0, 0] },
});
const colors = ['#42b883', '#ffb300', '#e57373', '#64b5f6', '#9575cd'];
const tweened = ref([...props.bars]);
watch(() => props.bars.slice(), (newData) => {
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
.stacked-bar-chart {
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
.stacked-bar {
  width: 100%;
  height: 32px;
  display: flex;
  border-radius: 8px;
  overflow: hidden;
  background: #e0e0e0;
  margin-bottom: 0.5rem;
}
.stacked-bar-segment {
  transition: width 0.3s;
  height: 100%;
}
.label {
  font-size: 1rem;
  color: #333;
}
</style>
