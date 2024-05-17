<script setup>
import { computed, onMounted, provide, reactive, ref, watch } from 'vue'
import axios from 'axios'

import Header from './components/Header.vue'
import CardList from './components/CardList.vue'
import Drawer from './components/Drawer.vue'

const items = ref([])
const cart = ref([])
const drawerOpen = ref(false)
const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))

const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

const createOrder = async () => {
  try {
    await axios.post('https://e0df4bb822e07583.mokky.dev/orders', {
      items: cart.value
    })
    cart.value = []
  } catch (error) {
    console.error(error)
  }
}

const addToCart = (item) => {
  if (!item.isAdded) {
    cart.value.push(item)
    item.isAdded = true
  } else {
    cart.value.splice(cart.value.indexOf(item), 1)
    item.isAdded = false
  }
}

const closeDrawer = () => {
  drawerOpen.value = false
}

const openDrawer = () => {
  drawerOpen.value = true
}

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value
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
  await fetchItems()
  await fetchFavorites()
})

watch(filters, fetchItems)

provide('cart', { cart, closeDrawer, addToCart })
</script>

<template>
  <div class="bg-white w-4/5 mt-14 mx-auto rounded-xl shadow-xl">
    <Header :total-price="totalPrice" @open-drawer="openDrawer" />

    <div class="p-10">
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
    </div>
  </div>

  <Drawer v-if="drawerOpen" :total-price="totalPrice" />
</template>

<style scoped></style>
