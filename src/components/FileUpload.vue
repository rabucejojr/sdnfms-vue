<template>
    <div class="file-upload">
      <input type="file" @change="handleFileUpload" />
      <button @click="submitFile" :disabled="!selectedFile">Upload</button>
      <p v-if="uploadStatus">{{ uploadStatus }}</p>
    </div>
  </template>
  
  <script>
  import { ref } from 'vue';
  
  export default {
    setup() {
      const selectedFile = ref(null);
      const uploadStatus = ref('');
  
      const handleFileUpload = (event) => {
        selectedFile.value = event.target.files[0];
      };
  
      const submitFile = async () => {
        if (!selectedFile.value) return;
  
        const formData = new FormData();
        formData.append('file', selectedFile.value);
  
        try {
          const response = await fetch('/api/upload', {
            method: 'POST',
            body: formData,
          });
  
          if (response.ok) {
            uploadStatus.value = 'File uploaded successfully!';
          } else {
            uploadStatus.value = 'File upload failed.';
          }
        } catch (error) {
          uploadStatus.value = 'An error occurred during file upload.';
        }
      };
  
      return {
        selectedFile,
        uploadStatus,
        handleFileUpload,
        submitFile,
      };
    },
  };
  </script>
  
  <style scoped>
  .file-upload {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
  }
  
  button {
    margin-top: 10px;
  }
  
  p {
    margin-top: 10px;
    color: green;
  }
  </style>
  