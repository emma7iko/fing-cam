<template>
  <div class="bar-chart">
    <div
      v-for="(bar, idx) in bars"
      :key="idx"
      class="bar-wrapper"
    >
      <transition name="bar-grow">
        <div
          class="bar"
          :key="bar + '-' + idx"
          :style="{
            height: bar ? '90%' : '12%',
            background: bar ? '#42b883' : '#e0e0e0',
            boxShadow: bar ? '0 4px 16px #42b88355' : 'none'
          }"
        >
          <span v-if="bar" class="bar-active-dot"></span>
        </div>
      </transition>
      <div class="label">{{ labels[idx] }}</div>
    </div>
  </div>
</template>

<script setup>

const props = defineProps({
  bars: {
    type: Array,
    default: () => [0, 0, 0, 0, 0],
  },
});
const labels = ['Thumb', 'Index', 'Middle', 'Ring', 'Pinky'];
</script>

<style scoped>
.bar-chart {
  display: flex;
  justify-content: space-around;
  align-items: flex-end;
  height: 28vw;
  min-height: 120px;
  max-height: 220px;
  background: #f4f4f4;
  padding: 1rem 0 0.5rem 0;
  border-radius: 0 0 14px 14px;
  box-shadow: 0 2px 8px #0001;
  width: 100%;
}
.bar-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 16vw;
  max-width: 60px;
  min-width: 36px;
  height: 100%;
}
.bar {
  width: 100%;
  min-height: 12px;
  border-radius: 10px 10px 0 0;
  transition: height 0.45s cubic-bezier(0.23,1,0.32,1), background 0.3s, box-shadow 0.3s;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  position: relative;
}
.bar-grow-enter-active, .bar-grow-leave-active {
  transition: all 0.45s cubic-bezier(0.23,1,0.32,1);
}
.bar-grow-enter-from, .bar-grow-leave-to {
  opacity: 0.7;
  transform: scaleY(0.8);
}
.bar-grow-enter-to, .bar-grow-leave-from {
  opacity: 1;
  transform: scaleY(1);
}

.bar-active-dot {
  position: absolute;
  top: 8px;
  left: 50%;
  transform: translateX(-50%);
  width: 14px;
  height: 14px;
  background: #fff;
  border-radius: 50%;
  border: 2px solid #42b883;
  box-shadow: 0 2px 8px #42b88344;
}
.label {
  margin-top: 0.5rem;
  font-size: 1rem;
  color: #333;
  text-align: center;
  word-break: keep-all;
}
@media (max-width: 600px) {
  .bar-chart {
    height: 34vw;
    min-height: 90px;
    max-height: 140px;
  }
  .bar-wrapper {
    width: 18vw;
    min-width: 24px;
    max-width: 40px;
  }
  .label {
    font-size: 0.8rem;
  }
}

.bar-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 40px;
}
.bar {
  width: 100%;
  transition: height 0.2s, background 0.2s;
  border-radius: 8px 8px 0 0;
  min-height: 16px;
}
.label {
  margin-top: 0.5rem;
  font-size: 0.9rem;
  color: #333;
}
</style>
