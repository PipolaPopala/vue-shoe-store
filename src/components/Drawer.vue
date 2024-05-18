<script setup>
import { computed, inject, ref } from 'vue'
import axios from 'axios'

import DrawerHead from './DrawerHead.vue'
import CartItemList from './CartItemList.vue'
import InfoBlock from './InfoBlock.vue'

const props = defineProps({
  totalPrice: Number
})

const isCreatingOrder = ref(false)
const orderId = ref(null)

const { cart, closeDrawer } = inject('cart')

const createOrder = async () => {
  try {
    isCreatingOrder.value = true
    const { data } = await axios.post('https://e0df4bb822e07583.mokky.dev/orders', {
      items: cart.value,
      totalPrice: props.totalPrice.value
    })

    cart.value = []

    orderId.value = data.id
    return data
  } catch (error) {
    console.error(error)
  } finally {
    isCreatingOrder.value = false
  }
}
</script>

<template>
  <div class="fixed top-0 left-0 w-full h-full bg-black bg-opacity-50 z-10"></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
    <DrawerHead />

    <div v-if="!totalPrice || orderId" class="flex h-full items-center">
      <InfoBlock
        v-if="!totalPrice && !orderId"
        title="Корзина пустая"
        description="Добавьте хотя бы одну пару кроссовок, чтобы оформить заказ"
        image-url="/package-icon.png"
      />
      <InfoBlock
        v-if="orderId"
        title="Заказ оформлен!"
        :description="`Ваш заказ #${orderId} скоро будет передан курьерской доставке`"
        image-url="/order-success-icon.png"
      />
    </div>

    <div v-else>
      <CartItemList />

      <div class="flex flex-col gap-4 mt-8">
        <div class="flex gap-2">
          <span>Итого:</span>
          <div class="flex-1 border-b border-dashed border-slate-200"></div>
          <b> {{ totalPrice }} руб.</b>
        </div>
        <div class="flex gap-2">
          <span>Налог 5%:</span>
          <div class="flex-1 border-b border-dashed border-slate-200"></div>
          <b>{{ totalPrice * 0.05 }} руб.</b>
        </div>
        <button
          @click="createOrder"
          class="mt-4 bg-lime-500 w-full rounded-xl py-3 text-white disabled:bg-slate-300 hover:bg-lime-600 active:bg-lime-700 transition cursor-pointer"
        >
          Оформить заказ
        </button>
      </div>
    </div>
  </div>
</template>
