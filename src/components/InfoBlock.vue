<script setup>
defineProps({
  title: String,
  description: String,
  imageUrl: String
})
</script>

<template>
  <div class="flex flex-col items-center text-center w-72 mx-auto">
    <img class="w-16 h-16" :src="imageUrl" alt="info image" />
    <h2 class="mt-2 text-2xl font-medium">{{ title }}</h2>
    <p class="text-slate-400">{{ description }}</p>
  </div>
</template>
