<script setup>
import Card from './Card.vue'

defineProps({
  items: Array
})

const emit = defineEmits(['addToFavorite'])
</script>

<template>
  <div class="grid sm:grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
    <Card
      v-for="item in items"
      :key="item.id"
      :id="item.id"
      :title="item.title"
      :imageUrl="item.imageUrl"
      :price="item.price"
      :onClickFavorite="() => emit('addToFavorite', item)"
      :isFavorite="item.isFavorite"
    />
  </div>
</template>
