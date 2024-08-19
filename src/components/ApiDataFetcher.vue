<template>
    <div class="api-data-fetcher">
      <h2>API Data</h2>
      <button @click="fetchData">Fetch Data</button>
  
      <div v-if="loading">Loading...</div>
      <div v-if="error" class="error">{{ error }}</div>
      
      <ul v-if="data.length">
        <li v-for="(item, index) in data" :key="index">
          {{ item }}
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  import { ref } from 'vue';
  
  export default {
    setup() {
      const data = ref([]);
      const loading = ref(false);
      const error = ref(null);
  
      const fetchData = async () => {
        loading.value = true;
        error.value = null;
        try {
          const response = await fetch('http://localhost:8000/api/fms/files/'); // Replace with your API endpoint
          if (!response.ok) {
            throw new Error('Failed to fetch data');
          }
          data.value = await response.json();
        } catch (err) {
          error.value = err.message;
        } finally {
          loading.value = false;
        }
      };
  
      return {
        data,
        loading,
        error,
        fetchData,
      };
    },
  };
  </script>
  
  <style scoped>
  .api-data-fetcher {
    text-align: left;
  }
  
  button {
    margin-bottom: 10px;
  }
  
  .error {
    color: red;
  }
  
  ul {
    list-style-type: none;
    padding: 0;
  }
  </style>
  