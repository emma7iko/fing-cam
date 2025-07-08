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


<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');

:root {
  --primary: #4f46e5;
  --primary-light: #818cf8;
  --dark: #1e293b;
  --light: #f8fafc;
  --gray-100: #f1f5f9;
  --gray-200: #e2e8f0;
  --gray-300: #cbd5e1;
  --gray-700: #334155;
  --shadow-sm: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
  --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1);
  --radius: 0.5rem;
  --transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', system-ui, -apple-system, sans-serif;
  background-color: var(--light);
  color: var(--dark);
  line-height: 1.5;
  -webkit-font-smoothing: antialiased;
}

.main-layout {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  background: linear-gradient(135deg, #f0f9ff 0%, #e0f2fe 100%);
  padding: 1rem;
  position: relative;
  overflow-x: hidden;
}

.main-header {
  text-align: center;
  padding: 1.5rem 0;
  position: relative;
  z-index: 10;
}

.main-header h1 {
  font-size: 2.5rem;
  font-weight: 800;
  letter-spacing: -0.025em;
  margin-bottom: 0.5rem;
  background: linear-gradient(to right, var(--primary), var(--primary-light));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  position: relative;
  display: inline-block;
}

.main-header h1::after {
  content: '';
  position: absolute;
  bottom: -8px;
  left: 50%;
  transform: translateX(-50%);
  width: 80px;
  height: 4px;
  background: linear-gradient(to right, var(--primary), var(--primary-light));
  border-radius: 2px;
}

.top-section.camera-card {
  background: white;
  border-radius: var(--radius);
  box-shadow: var(--shadow);
  padding: 1.5rem;
  margin: 0 auto 2rem;
  max-width: 1000px;
  width: 100%;
  position: relative;
  overflow: hidden;
  transition: var(--transition);
  border: 1px solid var(--gray-200);
}

.top-section.camera-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 4px;
  background: linear-gradient(to right, var(--primary), var(--primary-light));
}

.dashboard-section {
  flex: 1;
  width: 100%;
  max-width: 1400px;
  margin: 0 auto;
  padding: 0 1rem 2rem;
}

.dashboard-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1.5rem;
  width: 100%;
  margin-top: 1rem;
}

.dashboard-grid > * {
  background: white;
  border-radius: var(--radius);
  box-shadow: var(--shadow);
  padding: 1.5rem;
  transition: var(--transition);
  border: 1px solid var(--gray-200);
  position: relative;
  overflow: hidden;
}

.dashboard-grid > *::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 4px;
  background: var(--gray-200);
  transition: var(--transition);
}

.dashboard-grid > *:hover {
  transform: translateY(-4px);
  box-shadow: var(--shadow-lg);
}

.dashboard-grid > *:hover::before {
  background: linear-gradient(to right, var(--primary), var(--primary-light));
}

/* Responsive adjustments */
@media (max-width: 1024px) {
  .dashboard-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 768px) {
  .main-header h1 {
    font-size: 2rem;
  }
  
  .dashboard-grid {
    grid-template-columns: 1fr;
    gap: 1.25rem;
  }
  
  .top-section.camera-card {
    padding: 1rem;
    margin-bottom: 1.5rem;
  }
}

@media (max-width: 480px) {
  .main-layout {
    padding: 0.75rem;
  }
  
  .main-header {
    padding: 1rem 0;
  }
  
  .main-header h1 {
    font-size: 1.75rem;
  }
  
  .dashboard-section {
    padding: 0 0 1.5rem;
  }
}

/* Animation for cards */
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

.dashboard-grid > * {
  animation: fadeIn 0.4s ease-out forwards;
  opacity: 0;
}

.dashboard-grid > *:nth-child(1) { animation-delay: 0.1s; }
.dashboard-grid > *:nth-child(2) { animation-delay: 0.2s; }
.dashboard-grid > *:nth-child(3) { animation-delay: 0.3s; }
.dashboard-grid > *:nth-child(4) { animation-delay: 0.4s; }
.dashboard-grid > *:nth-child(5) { animation-delay: 0.5s; }

</style>
