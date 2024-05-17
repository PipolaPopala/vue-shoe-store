<script setup>
import { computed, provide, ref, watch } from 'vue'
import axios from 'axios'

import Header from './components/Header.vue'
import Drawer from './components/Drawer.vue'

/* Корзина (start)*/
const cart = ref([])
const drawerOpen = ref(false)
const isCreatingOrder = ref(false)

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))
const cartButtonDisabled = computed(() => !totalPrice.value || isCreatingOrder.value)

const createOrder = async () => {
  try {
    isCreatingOrder.value = true
    const { data } = await axios.post('https://e0df4bb822e07583.mokky.dev/orders', {
      items: cart.value,
      totalPrice: totalPrice.value
    })

    cart.value = []
    return data
  } catch (error) {
    console.error(error)
  } finally {
    isCreatingOrder.value = false
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

  <Drawer
    v-if="drawerOpen"
    :total-price="totalPrice"
    :button-disabled="cartButtonDisabled"
    @create-order="createOrder"
  />
</template>

<style scoped></style>
