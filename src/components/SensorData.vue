<template>
  <div>
    <h2>📊 센서 데이터</h2>

    <select v-model="selectedCollection" @change="loadData">
      <option value="First_Car">First_Car</option>
      <option value="Second_Car">Second_Car</option>
    </select>

    <line-chart :chart-data="tempChartData" title="온도 (°C)" />
    <line-chart :chart-data="humiChartData" title="습도 (%)" />
    <line-chart :chart-data="gasChartData" title="가스 (단위 없음)" />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { db } from '../firebase'
import { collection, getDocs } from 'firebase/firestore'
import LineChart from './LineChart.vue'

const selectedCollection = ref('First_Car')
const rawData = ref([])

const tempChartData = ref(null)
const humiChartData = ref(null)
const gasChartData = ref(null)

const loadData = async () => {
  rawData.value = []
  const querySnapshot = await getDocs(collection(db, selectedCollection.value))
  querySnapshot.forEach((doc) => {
    rawData.value.push({
      id: doc.id,
      ...doc.data()
    })
  })

  const labels = rawData.value.map(item =>
    item.time?.toDate().toLocaleTimeString()
  )

  tempChartData.value = {
    labels,
    datasets: [{
      label: '온도 (°C)',
      data: rawData.value.map(item => item.temp),
      fill: false,
      borderColor: 'red',
      tension: 0.1
    }]
  }

  humiChartData.value = {
    labels,
    datasets: [{
      label: '습도 (%)',
      data: rawData.value.map(item => item.humi),
      fill: false,
      borderColor: 'blue',
      tension: 0.1
    }]
  }

  gasChartData.value = {
    labels,
    datasets: [{
      label: '가스',
      data: rawData.value.map(item => item.gas),
      fill: false,
      borderColor: 'green',
      tension: 0.1
    }]
  }
}

onMounted(loadData)
</script>

<style scoped>
h2 {
  color: #2c3e50;
  margin-bottom: 1rem;
}
select {
  margin-bottom: 2rem;
}
</style>
