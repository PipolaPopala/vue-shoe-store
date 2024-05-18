<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

import CardList from '../components/CardList.vue'

const favorites = ref([])

onMounted(async () => {
  try {
    const { data } = await axios.get(
      'https://e0df4bb822e07583.mokky.dev/favorites?_relations=items'
    )

    favorites.value = data.map((obj) => obj.item)
  } catch (error) {
    console.error(error)
  }
})
</script>

<template>
  <h1 class="text-3xl font-bold">Мои закладки</h1>
  <div class="mt-10">
    <CardList :items="favorites" is-favorites />
  </div>
</template>
