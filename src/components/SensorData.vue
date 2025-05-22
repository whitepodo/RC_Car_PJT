<template>
  <div>
    <h2>📊 센서 데이터</h2>
    <select v-model="selectedCar" @change="fetchData">
      <option value="First_Car">First_Car</option>
      <option value="Second_Car">Second_Car</option>
    </select>
    <LineChart :chartData="chartData" :chartOptions="chartOptions" />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { collection, getDocs } from 'firebase/firestore'
import { db } from '../firebase'
import LineChart from './LineChart.vue'

const selectedCar = ref('First_Car')
const chartData = ref({
  labels: [],
  datasets: []
})
const chartOptions = ref({
  responsive: true,
  plugins: {
    legend: { position: 'top' },
    title: { display: true, text: '센서 데이터 시간별 그래프' }
  }
})

const fetchData = async () => {
  const querySnapshot = await getDocs(collection(db, selectedCar.value))
  const docs = []
  querySnapshot.forEach(doc => docs.push(doc.data()))

  // 시간순 정렬
  docs.sort((a, b) => new Date(a.time) - new Date(b.time))

  chartData.value = {
    labels: docs.map(d => new Date(d.time).toLocaleTimeString()),
    datasets: [
      {
        label: '온도 (℃)',
        data: docs.map(d => d.temp),
        borderColor: 'red',
        tension: 0.2
      },
      {
        label: '습도 (%)',
        data: docs.map(d => d.humi),
        borderColor: 'blue',
        tension: 0.2
      },
      {
        label: '가스',
        data: docs.map(d => d.gas),
        borderColor: 'green',
        tension: 0.2
      }
    ]
  }
}

onMounted(fetchData)
</script>
