<template>
  <div>
    <div class="mb-4">
      <label class="block text-sm font-medium text-gray-700">Select Page Range (1 - {{ totalPages }})</label>
      <div class="flex space-x-2 mt-1">
        <input
            :value="startPage"
            @input="updateStartPage($event.target.value)"
            type="number"
            :min="1"
            :max="totalPages"
            placeholder="Start Page"
            class="w-1/2 p-2 border rounded text-sm"
        >
        <input
            :value="endPage"
            @input="updateEndPage($event.target.value)"
            type="number"
            :min="1"
            :max="totalPages"
            placeholder="End Page"
            class="w-1/2 p-2 border rounded text-sm"
        >
      </div>
    </div>
    <div class="mb-4">
      <label class="block text-sm font-medium text-gray-700">Output Format</label>
      <select
          :value="outputFormat"
          @change="$emit('update:outputFormat', $event.target.value)"
          class="mt-1 block w-full p-2 border rounded text-sm"
      >
        <option value="png">PNG</option>
        <option value="jpg">JPG</option>
      </select>
    </div>
  </div>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue';

const props = defineProps({
  totalPages: Number,
  startPage: Number,
  endPage: Number,
  outputFormat: String,
});

const emit = defineEmits(['update:startPage', 'update:endPage', 'update:outputFormat']);

const updateStartPage = (value) => {
  let newValue = parseInt(value, 10);
  if (isNaN(newValue) || newValue < 1) newValue = 1;
  if (newValue > props.totalPages) newValue = props.totalPages;
  emit('update:startPage', newValue);
};

const updateEndPage = (value) => {
  let newValue = parseInt(value, 10);
  if (isNaN(newValue) || newValue < 1) newValue = 1;
  if (newValue > props.totalPages) newValue = props.totalPages;
  emit('update:endPage', newValue);
};
</script>