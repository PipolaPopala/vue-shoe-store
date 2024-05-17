<script setup>
const emit = defineEmits(['addToCart'])

defineProps({
  id: Number,
  title: String,
  imageUrl: String,
  price: Number
})
</script>

<template>
  <div class="flex items-center border border-slate-200 p-4 rounded-xl gap-4">
    <img :src="imageUrl" alt="sneaker" class="w-16 h-16" />
    <div class="flex flex-col flex-1">
      <p>{{ title }}</p>
      <div class="flex justify-between mt-2">
        <b class="flex-1">{{ price }}</b>
        <img
          src="/close.svg"
          alt="delete item image"
          class="cursor-pointer opacity-60 hover:opacity-100 transition"
          @click="emit('addToCart')"
        />
      </div>
    </div>
  </div>
</template>
