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
  <div class="main-layout">
    <header class="main-header">
      <h1>🖐️ Fing-Cam</h1>
      <!-- <span class="subtitle">Live hand tracking & animated finger analytics</span> -->
    </header>
    <div class="top-section camera-card">
      <CameraHand @fingerStatus="updateBars" />
    </div>
    <div class="dashboard-section">
      <div class="dashboard-grid">
        <FingerBarChart :bars="fingerBars" />
        <FingerLineChart :data="fingerBars" />
        <FingerPieChart :data="fingerBars" />
        <FingerRadarChart :data="fingerBars" />
        <FingerStackedBarChart :bars="fingerBars" />
      </div>
    </div>
  </div>
</template>


<style scoped>
.main-layout {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  background: linear-gradient(135deg, #f7fafc 0%, #e3eafc 100%);
  font-family: 'Inter', 'Segoe UI', Arial, sans-serif;
}
.main-header {
  width: 100%;
  text-align: center;
  padding: 2.2rem 0 0.7rem 0;
  background: none;
}
.main-header h1 {
  font-size: 2.3rem;
  font-weight: 800;
  letter-spacing: -1px;
  color: #2a395b;
  margin-bottom: 0.25rem;
  background: linear-gradient(90deg, #5a8dee 10%, #42b883 90%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
.main-header .subtitle {
  font-size: 1.1rem;
  color: #6b7bbd;
  opacity: 0.88;
  font-weight: 500;
  letter-spacing: 0.03em;
}
.top-section.camera-card {
  flex: 1 1 auto;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(34,34,34,0.85);
  min-height: 260px;
  margin: 0 auto 1.5rem auto;
  max-width: 900px;
  border-radius: 22px;
  box-shadow: 0 6px 36px #5a8dee22, 0 1.5px 6px #2a395b22;
  padding: 2vw 0 1vw 0;
}

.dashboard-section {
  width: 100%;
  background: rgba(255,255,255,0.7);
  box-shadow: 0 -2px 16px #b3c0e633;
  padding: 2vw 2vw 2vw 2vw;
  display: flex;
  flex-direction: column;
  align-items: center;
  border-radius: 24px 24px 0 0;
  backdrop-filter: blur(10px);
  margin-top: -2vw;
}
.dashboard-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 2.2rem;
  justify-content: center;
  align-items: flex-start;
  width: 100%;
  max-width: 1200px;
}
.dashboard-grid > * {
  background: rgba(255,255,255,0.92);
  border-radius: 18px;
  box-shadow: 0 4px 24px #b3c0e644, 0 1.5px 4px #b3c0e622;
  padding: 1.2rem 1.1rem 0.7rem 1.1rem;
  min-width: 210px;
  min-height: 150px;
  transition: box-shadow 0.22s cubic-bezier(.4,1,.7,1), transform 0.18s cubic-bezier(.4,1,.7,1);
  margin-bottom: 1.2rem;
  position: relative;
}
.dashboard-grid > *:hover {
  box-shadow: 0 8px 32px #6b7bbd55, 0 2.5px 8px #b3c0e655;
  transform: translateY(-2px) scale(1.025);
  z-index: 2;
}

@media (max-width: 900px) {
  .dashboard-grid {
    gap: 1rem;
  }
}
@media (max-width: 600px) {
  .main-layout {
    min-height: 100svh;
  }
  .top-section {
    min-height: 120px;
    padding: 1vw 0 2vw 0;
  }
  .dashboard-section {
    padding: 1vw 1vw 1vw 1vw;
  }
  .dashboard-grid {
    flex-direction: column;
    align-items: center;
    gap: 0.7rem;
  }
}

</style>
