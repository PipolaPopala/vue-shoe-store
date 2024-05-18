<script setup>
import { computed, provide, ref, watch } from 'vue'
import axios from 'axios'

import Header from './components/Header.vue'
import Drawer from './components/Drawer.vue'

/* Корзина (start)*/
const cart = ref([])
const drawerOpen = ref(false)

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))

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

watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  { deep: true }
)

provide('cart', { cart, closeDrawer, addToCart })
/* Корзина (end)*/
</script>

<template>
  <div class="bg-white w-4/5 mt-14 mx-auto rounded-xl shadow-xl">
    <Header :total-price="totalPrice" @open-drawer="openDrawer" />

    <div class="p-10">
      <router-view></router-view>
    </div>
  </div>

  <Drawer v-if="drawerOpen" :total-price="totalPrice" />
</template>

<style scoped></style>
