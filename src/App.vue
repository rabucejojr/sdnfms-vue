<template>
  <div class="p-6">
    <div class="flex space-x-6">
      <!-- File Upload Component -->
      <div class="flex-1">
        <FileUpload :editData="editData" @clearEdit="clearEdit" />
      </div>

      <!-- Data Table Component -->
      <div class="flex-1">
        <FileTable @edit="handleEdit" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import FileUpload from './components/FileUpload.vue';
import FileTable from './components/FileTable.vue';

// Define reactive properties
const editData = ref(null);

// Handle edit event from DataTable
function handleEdit(file) {
  editData.value = file;
}

// Clear edit data (optional, for instance, after successful update)
function clearEdit() {
  editData.value = null;
}
</script>

<style scoped>
/* No additional styles needed as we're using Tailwind CSS */
</style>
