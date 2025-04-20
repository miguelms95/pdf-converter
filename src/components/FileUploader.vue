<template>
  <div class="mb-4">
    <label class="block text-sm font-medium text-gray-700 mb-2">Upload PDF</label>
    <Transition name="drag">
      <div
          class="relative border-2 border-dashed rounded-lg p-6 text-center transition-all duration-300"
          :class="{
          'border-blue-500 bg-blue-50 scale-105': isDragging,
          'border-gray-300 bg-gray-50': !isDragging
        }"
          @dragover.prevent="handleDragOver"
          @dragenter.prevent="handleDragEnter"
          @dragleave.prevent="handleDragLeave"
          @drop.prevent="handleDrop"
      >
        <input
            type="file"
            accept="application/pdf"
            ref="fileInput"
            class="hidden"
            @change="onFileChange"
        >
        <p class="text-gray-600">
          <span v-if="isDragging" class="font-semibold text-blue-600">Drop your PDF here!</span>
          <span v-else>Drag and drop a PDF here or <span class="text-blue-600 cursor-pointer hover:underline" @click="triggerFileInput">click to upload</span></span>
        </p>
      </div>
    </Transition>
    <p v-if="fileName" class="mt-2 text-sm text-gray-700 bg-gray-100 p-2 rounded">
      File: <span class="font-semibold">{{ fileName }}</span>
    </p>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { defineEmits, defineProps } from 'vue';

const props = defineProps({
  fileName: String,
});

const emit = defineEmits(['file-uploaded']);
const isDragging = ref(false);
const fileInput = ref(null);

const handleDragOver = () => {
  isDragging.value = true;
};

const handleDragEnter = () => {
  isDragging.value = true;
};

const handleDragLeave = () => {
  isDragging.value = false;
};

const handleDrop = (event) => {
  isDragging.value = false;
  const file = event.dataTransfer.files[0];
  if (file) {
    emit('file-uploaded', file);
  }
};

const onFileChange = (event) => {
  const file = event.target.files[0];
  if (file) {
    emit('file-uploaded', file);
  }
};

const triggerFileInput = () => {
  fileInput.value.click();
};
</script>

<style scoped>
.drag-enter,
.drag-leave {
  transition: all 0.3s ease;
}

.drag-enter {
  transform: scale(1.05);
  background-color: #eff6ff;
  border-color: #3b82f6;
}

.drag-leave {
  transform: scale(1);
  background-color: #f9fafb;
  border-color: #d1d5db;
}
</style>