<script setup>
import { inject, onMounted, reactive, ref, watch } from 'vue'
import CardList from '../components/CardList.vue'
import axios from 'axios'

const items = ref([])

const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

const { cart, addToCart } = inject('cart')

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value
}

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id
      }
      const { data } = await axios.post('https://e0df4bb822e07583.mokky.dev/favorites', obj)
      item.isFavorite = true
      item.favoriteId = data.id
      console.log(item)
    } else {
      await axios.delete(`https://e0df4bb822e07583.mokky.dev/favorites/${item.favoriteId}`)
      item.isFavorite = false
      item.favoriteId = null
    }
  } catch (error) {
    console.error(error)
  }
}

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get('https://e0df4bb822e07583.mokky.dev/favorites')
    items.value = items.value.map((item) => {
      const favorite = favorites.some((favorite) => favorite.parentId === item.id)
      if (!favorite) {
        return item
      }
      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      }
    })
  } catch (error) {
    console.error(error)
  }
}

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy
    }

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }

    const { data } = await axios.get('https://e0df4bb822e07583.mokky.dev/items', { params })

    items.value = data.map((item) => ({
      ...item,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (error) {
    console.error(error)
  }
}

onMounted(async () => {
  cart.value = JSON.parse(localStorage.getItem('cart') || '[]')

  await fetchItems()
  await fetchFavorites()

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))
})

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})

watch(filters, fetchItems)
</script>

<template>
  <div class="flex justify-between items-center mb-8">
    <h2 class="text-3xl font-bold">Все кроссовки</h2>
    <div class="flex gap-4">
      <select @change="onChangeSelect" class="py-2 px-4 border rounded outline-none">
        <option value="name">По названию</option>
        <option value="price">По цене (дешевые)</option>
        <option value="-price">По цене (дорогие)</option>
      </select>
      <div class="relative">
        <img src="/search.svg" alt="search image" class="absolute top-3 left-4" />
        <input
          @input="onChangeSearchInput"
          type="text"
          placeholder="Поиск..."
          class="border rounded-xl py-2 pl-12 pr-4 outline-none focus:border-gray-400"
        />
      </div>
    </div>
  </div>
  <div class="mt-10">
    <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="addToCart" />
  </div>
</template>
