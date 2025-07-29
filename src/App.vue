<script setup>
import { ref } from 'vue';
import CameraHand from './components/CameraHand.vue';
import FingerBarChart from './components/FingerBarChart.vue';
import FingerLineChart from './components/FingerLineChart.vue';
import FingerPieChart from './components/FingerPieChart.vue';
import FingerRadarChart from './components/FingerRadarChart.vue';
import FingerStackedBarChart from './components/FingerStackedBarChart.vue';

const fingerBars = ref([0, 0, 0, 0, 0]);
function updateBars(status) {
  fingerBars.value = status;
}
</script>

<template>
  <div id="app">
    <header class="main-header">
      <h1>🖐️ Fing-Cam</h1>
    </header>
    <main class="main-content">
      <div class="integrated-grid">
        <div class="chart-card tall-chart">
          <FingerPieChart :data="fingerBars" />
        </div>
        <div class="chart-card tall-chart">
          <FingerBarChart :bars="fingerBars" />
        </div>
        
        <section class="camera-card-container">
          <div class="camera-card">
            <CameraHand @fingerStatus="updateBars" />
          </div>
        </section>
        
        <div class="chart-card tall-chart">
          <FingerRadarChart :data="fingerBars" />
        </div>
        <div class="chart-card tall-chart">
          <FingerLineChart :data="fingerBars" />
        </div>
        
        <div class="chart-card wide-chart">
          <FingerStackedBarChart :bars="fingerBars" />
        </div>
      </div>
    </main>
  </div>
</template>





