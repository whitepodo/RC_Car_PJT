<template>
  <div>
    <h2>📊 센서 데이터</h2>

    <select v-model="selectedCollection" @change="loadData">
      <option value="First_Car">First_Car</option>
      <option value="Second_Car">Second_Car</option>
    </select>

    <ul>
      <li v-for="item in sensorData" :key="item.id">
        🕒 {{ item.time?.toDate().toLocaleString() }} |
        🌡️ 온도: {{ item.temp }} |
        💧 습도: {{ item.humi }} |
        🪨 가스: {{ item.gas }}
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { db } from '../firebase'
import { collection, getDocs } from 'firebase/firestore'

const selectedCollection = ref('First_Car')
const sensorData = ref([])

const loadData = async () => {
  sensorData.value = []
  const querySnapshot = await getDocs(collection(db, selectedCollection.value))
  querySnapshot.forEach((doc) => {
    sensorData.value.push({
      id: doc.id,
      ...doc.data()
    })
  })
}

onMounted(() => {
  loadData()
})
</script>

<style scoped>
h2 {
  color: #2c3e50;
  margin-bottom: 1rem;
}
li {
  margin: 0.5rem 0;
}
select {
  margin-bottom: 1rem;
}
</style>