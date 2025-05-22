<template>
  <div>
    <h3>{{ title }}</h3>
    <canvas ref="canvasRef"></canvas>
  </div>
</template>

<script setup>
import { onMounted, watch, ref } from 'vue'
import {
  Chart,
  LineController,
  LineElement,
  PointElement,
  LinearScale,
  Title,
  CategoryScale
} from 'chart.js'

Chart.register(LineController, LineElement, PointElement, LinearScale, Title, CategoryScale)

const props = defineProps({
  chartData: Object,
  title: String
})

const canvasRef = ref(null)
let chartInstance = null

const renderChart = () => {
  if (chartInstance) {
    chartInstance.destroy()
  }

  chartInstance = new Chart(canvasRef.value, {
    type: 'line',
    data: props.chartData,
    options: {
      responsive: true,
      maintainAspectRatio: false,
      scales: {
        y: {
          beginAtZero: true
        }
      }
    }
  })
}

watch(() => props.chartData, () => {
  if (props.chartData) renderChart()
})
</script>

<style scoped>
div {
  margin-bottom: 3rem;
  height: 300px;
}
h3 {
  margin-bottom: 0.5rem;
}
</style>
