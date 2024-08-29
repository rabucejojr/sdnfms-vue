<template>
    <div class="p-6 bg-white border border-gray-300 rounded-lg shadow-md">
      <h2 class="text-2xl font-semibold mb-4">Data Table with Pagination</h2>
  
      <div class="overflow-x-auto">
        <table class="min-w-full bg-white border border-gray-300 rounded-lg shadow-md">
          <thead class="bg-gray-100 border-b border-gray-300">
            <tr>
              <th class="px-6 py-3 text-left text-gray-600">File Name</th>
              <th class="px-6 py-3 text-left text-gray-600">File Type</th>
              <th class="px-6 py-3 text-left text-gray-600">File Size</th>
              <th class="px-6 py-3 text-left text-gray-600">Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="file in files" :key="file.id" class="border-b border-gray-300">
              <td class="px-6 py-4 text-gray-700">{{ file.name }}</td>
              <td class="px-6 py-4 text-gray-700">{{ file.type }}</td>
              <td class="px-6 py-4 text-gray-700">{{ formatFileSize(file.size) }}</td>
              <td class="px-6 py-4">
                <button @click="editFile(file)" class="text-blue-500 hover:underline">Edit</button>
                <button @click="deleteFile(file.id)" class="text-red-500 hover:underline ml-4">Delete</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
  
      <!-- Pagination Controls -->
      <div class="flex justify-between items-center mt-4">
        <button 
          @click="prevPage" 
          :disabled="currentPage === 1"
          class="px-4 py-2 bg-gray-300 text-gray-700 rounded hover:bg-gray-400 disabled:opacity-50"
        >
          Previous
        </button>
        <span class="text-gray-700">Page {{ currentPage }} of {{ totalPages }}</span>
        <button 
          @click="nextPage" 
          :disabled="currentPage === totalPages"
          class="px-4 py-2 bg-gray-300 text-gray-700 rounded hover:bg-gray-400 disabled:opacity-50"
        >
          Next
        </button>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import axios from 'axios';
  import Swal from 'sweetalert2';
  
  const files = ref([]);
  const currentPage = ref(1);
  const totalPages = ref(1);
  const perPage = 10; // Number of items per page
  
  const emit = defineEmits(['edit']);
  
  async function fetchFiles(page = 1) {
    try {
      const response = await axios.get(`http://127.0.0.1:8000/api/fms/files?page=${page}&per_page=${perPage}`);
      files.value = response.data.data;
      totalPages.value = Math.ceil(response.data.total / perPage);
    } catch (error) {
      Swal.fire({
        icon: 'error',
        title: 'Failed to fetch data',
        text: 'There was an error fetching the files.',
      });
      console.error(error);
    }
  }
  
  function formatFileSize(size) {
    if (size < 1024) return `${size} bytes`;
    else if (size < 1048576) return `${(size / 1024).toFixed(2)} KB`;
    else return `${(size / 1048576).toFixed(2)} MB`;
  }
  
  function editFile(file) {
    emit('edit', file); // Emit file data to parent component
  }
  
  async function deleteFile(fileId) {
    try {
      await axios.delete(`http://127.0.0.1:8000/api/fms/files/${fileId}`);
      Swal.fire({
        icon: 'success',
        title: 'File Deleted',
        text: 'The file was successfully deleted.',
      });
      fetchFiles(currentPage.value); // Refresh the list
    } catch (error) {
      Swal.fire({
        icon: 'error',
        title: 'Delete Failed',
        text: 'Failed to delete the file.',
      });
      console.error(error);
    }
  }
  
  // Pagination handlers
  function prevPage() {
    if (currentPage.value > 1) {
      currentPage.value--;
      fetchFiles(currentPage.value);
    }
  }
  
  function nextPage() {
    if (currentPage.value < totalPages.value) {
      currentPage.value++;
      fetchFiles(currentPage.value);
    }
  }
  
  onMounted(() => fetchFiles(currentPage.value));
  </script>
  
  <style scoped>
  /* No additional styles needed as we're using Tailwind CSS */
  </style>
  