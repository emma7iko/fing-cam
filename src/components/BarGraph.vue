<template>
  <div class="bar-graph">
    <div v-for="(bar, idx) in bars" :key="bar.name" class="bar-container">
      <div class="bar-label">{{ bar.name }}</div>
      <div class="bar-outer">
        <div class="bar-inner" :style="{ height: (bar.value * 100) + '%' }"></div>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({ bars: Array })
</script>

<style scoped>
.bar-graph {
  display: flex;
  flex-direction: column;
  gap: 18px;
  min-width: 120px;
}
.bar-container {
  display: flex;
  align-items: center;
  gap: 12px;
}
.bar-label {
  width: 70px;
  font-size: 1em;
  text-align: right;
}
.bar-outer {
  width: 30px;
  height: 60px;
  background: black;
  border-radius: 7px;
  overflow: hidden;
  display: flex;
  align-items: flex-end;
}
.bar-inner {
  width: 100%;
  background: aqua;
  border-radius: 7px 7px 0 0;
  transition: height 0.3s cubic-bezier(.4,2,.6,1);
}
</style>
