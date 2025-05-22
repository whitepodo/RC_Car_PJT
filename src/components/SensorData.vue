<template>
  <div>
    <h2>📊 센서 데이터</h2>
    <ul>
      <li v-for="item in sensorData" :key="item.id">
        🕒 {{ item.timestamp?.toDate().toLocaleString() }} |
        🌡 온도: {{ item.temp }} |
        💧 습도: {{ item.humi }} |
        🧪 가스: {{ item.gas }}
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { db } from '../firebase'
import { collection, getDocs } from 'firebase/firestore'

const sensorData = ref([])

onMounted(async () => {
  const querySnapshot = await getDocs(collection(db, 'esp32'))
  querySnapshot.forEach((doc) => {
    sensorData.value.push({
      id: doc.id,
      ...doc.data()
    })
  })
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
</style>
