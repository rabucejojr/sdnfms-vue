<template>
    <div class="p-6 bg-white border border-gray-300 rounded-lg shadow-md">
      <h2 class="text-2xl font-semibold mb-4">File Upload Component</h2>
  
      <!-- Textbox -->
      <input 
        type="text" 
        v-model="fileName" 
        placeholder="Enter file name" 
        class="w-full p-2 mb-4 border border-gray-300 rounded"
      />
  
      <!-- File Upload -->
      <input 
        type="file" 
        @change="handleFileUpload" 
        class="w-full p-2 mb-4 border border-gray-300 rounded"
      />
  
      <!-- Upload Button -->
      <button @click="uploadFile" class="w-full p-2 mb-4 bg-blue-600 text-white rounded hover:bg-blue-700">
        {{ isEdit ? 'Update' : 'Upload' }}
      </button>
  
      <!-- Display Selected File Information -->
      <div v-if="file">
        <p class="text-gray-700">Selected file: {{ file.name }}</p>
        <p class="text-gray-700">File size: {{ file.size }} bytes</p>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, watch } from 'vue';
  import axios from 'axios';
  import Swal from 'sweetalert2';
  
  const props = defineProps({
    editData: Object
  });
  
  const emit = defineEmits(['clearEdit']);
  
  const fileName = ref('');
  const file = ref(null);
  const isEdit = ref(false);
  // Watch for changes in editData prop to populate fields
  watch(() => props.editData, (newData) => {
    if (newData) {
      fileName.value = newData.name;
      // Note: You will need to fetch the file if it's a URL or path
      file.value = null; // Handle file fetching if necessary
      isEdit.value = true;
    } else {
      fileName.value = '';
      file.value = null;
      isEdit.value = false;
    }
  });
  
  // Handle file upload
  function handleFileUpload(event) {
    const selectedFile = event.target.files[0];
    if (selectedFile) {
      file.value = selectedFile;
    }
  }
  
  // Handle file upload or update to API endpoint
  async function uploadFile() {
    if (!file.value && !isEdit.value) {
      Swal.fire({
        icon: 'warning',
        title: 'No file selected',
        text: 'Please select a file to upload.',
      });
      return;
    }
  
    const formData = new FormData();
    formData.append('name', fileName.value);
    if (file.value) formData.append('file', file.value);
  
    try {
      const url = isEdit.value
        ? `https://http://127.0.0.1:8000/api/fms/files/${props.editData.id}`
        : 'http://127.0.0.1:8000/api/fms/files';
  
      const method = isEdit.value ? 'put' : 'post';
  
      const response = await axios[method](url, formData, {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      });
  
      Swal.fire({
        icon: 'success',
        title: isEdit.value ? 'Update Successful' : 'Upload Successful',
        text: isEdit.value ? 'File updated successfully!' : 'File uploaded successfully!',
      });
      
      emit('clearEdit'); // Clear edit mode after successful update
      console.log(response.data);
    } catch (error) {
      Swal.fire({
        icon: 'error',
        title: isEdit.value ? 'Update Failed' : 'Upload Failed',
        text: isEdit.value ? 'Failed to update file.' : 'Failed to upload file.',
      });
      console.error(error);
    }
  }
  </script>
  
  <style scoped>
  /* No additional styles needed as we're using Tailwind CSS */
  </style>
  