<template>
  <button
      @click="$emit('convert')"
      :disabled="isConverting"
      class="w-full bg-blue-600 text-white p-2 rounded hover:bg-blue-700 disabled:bg-gray-400"
  >
    {{ isConverting ? 'Converting...' : 'Convert and Download' }}
  </button>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue';

defineProps({
  isConverting: Boolean,
});

defineEmits(['convert']);
</script>