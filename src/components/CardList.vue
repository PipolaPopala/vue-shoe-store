<script setup>
import Card from './Card.vue'

const onClickAdd = () => {
  alert('Добавлено')
}
</script>

<template>
  <div class="grid sm:grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
    <Card
      title="Мужские кроссовки"
      imageUrl="/sneakers/sneakers-1.jpg"
      :price="5000"
      isFavorite
      isAdded
      :onClickAdd="onClickAdd"
    />
  </div>
</template>
