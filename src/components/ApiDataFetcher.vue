<template>
  <div>
    <h1>API Data Fetcher</h1>
    <div v-if="loading">Loading...</div>
    <div v-else-if="error">{{ error }}</div>
    <div v-else>
      <ul>
        <li v-for="item in items" :key="item.id">{{ item.name }}</li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const items = ref([]);
const loading = ref(true);
const error = ref(null);

const fetchData = async () => {
  try {
    const response = await axios.get('http://localhost:8000/api/fms/files');
    items.value = response.data;
  } catch (err) {
    error.value = 'Failed to load data';
  } finally {
    loading.value = false;
  }
};

onMounted(fetchData);
</script>
