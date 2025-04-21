<template>
  <div class="min-h-screen bg-gray-100 flex items-center justify-center flex-col p-4">
    <div class="bg-white p-6 rounded-lg shadow-lg w-full max-w-lg">
      <h1 class="text-2xl font-bold mb-4 text-center">📄 PDF to Image Converter</h1>
      <FileUploader @file-uploaded="handleFileUpload" :file-name="fileName" />
      <PageSelector
          v-if="totalPages > 0"
          :total-pages="totalPages"
          v-model:start-page="startPage"
          v-model:end-page="endPage"
          v-model:output-format="outputFormat"
      />
      <ConvertButton
          v-if="totalPages > 0"
          :is-converting="isConverting"
          @convert="convertPages"
      />
      <p v-if="error" class="mt-4 text-red-600 text-sm">{{ error }}</p>
    </div>
    <p class="text-sm text-gray-400 p-4 mt-4">developed by <a class="hover:text-sky-600 transition-colors duration-300" href="https://miguelms.es" target="_blank">miguelms.es</a></p>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import FileUploader from './components/FileUploader.vue';
import PageSelector from './components/PageSelector.vue';
import ConvertButton from './components/ConvertButton.vue';
import * as pdfjsLib from 'pdfjs-dist';

// Set up pdf.js worker
pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdn.jsdelivr.net/npm/pdfjs-dist@3.11.174/build/pdf.worker.min.js';

const pdfFile = ref(null);
const fileName = ref('');
const totalPages = ref(0);
const startPage = ref(1);
const endPage = ref(1);
const outputFormat = ref('png');
const isConverting = ref(false);
const error = ref('');

// Handle file upload
const handleFileUpload = async (file) => {
  error.value = '';
  if (!file || file.type !== 'application/pdf') {
    error.value = 'Please upload a valid PDF file.';
    fileName.value = '';
    return;
  }
  pdfFile.value = file;
  fileName.value = file.name;
  await loadPDF(file);
};

// Load PDF and get total pages
const loadPDF = async (file) => {
  try {
    const arrayBuffer = await file.arrayBuffer();
    const pdf = await pdfjsLib.getDocument({ data: arrayBuffer }).promise;
    totalPages.value = pdf.numPages;
    // Only reset page range if the current range is invalid
    if (startPage.value > pdf.numPages || endPage.value > pdf.numPages || startPage.value > endPage.value) {
      startPage.value = 1;
      endPage.value = 1;
    }
  } catch (err) {
    error.value = 'Failed to load PDF. Please try again.';
    fileName.value = '';
    console.error(err);
  }
};

// Convert selected pages to images
const convertPages = async () => {
  if (!pdfFile.value) {
    error.value = 'No PDF uploaded.';
    return;
  }

  if (
      startPage.value < 1 ||
      endPage.value > totalPages.value ||
      startPage.value > endPage.value
  ) {
    error.value = 'Invalid page range. Ensure start page is less than or equal to end page and within total pages.';
    return;
  }

  isConverting.value = true;
  error.value = '';

  try {
    const arrayBuffer = await pdfFile.value.arrayBuffer();
    const pdf = await pdfjsLib.getDocument({ data: arrayBuffer }).promise;

    for (let pageNum = startPage.value; pageNum <= endPage.value; pageNum++) {
      const page = await pdf.getPage(pageNum);
      const viewport = page.getViewport({ scale: 2.0 });

      const canvas = document.createElement('canvas');
      canvas.width = viewport.width;
      canvas.height = viewport.height;
      const context = canvas.getContext('2d');

      await page.render({ canvasContext: context, viewport }).promise;

      const mimeType = outputFormat.value === 'png' ? 'image/png' : 'image/jpeg';
      const imgData = canvas.toDataURL(mimeType, outputFormat.value === 'jpg' ? 0.9 : 1.0);

      const link = document.createElement('a');
      link.href = imgData;
      link.download = `page_${pageNum}.${outputFormat.value}`;
      link.click();
    }
  } catch (err) {
    error.value = 'Failed to convert PDF. Please try again.';
    console.error(err);
  } finally {
    isConverting.value = false;
  }
};
</script>