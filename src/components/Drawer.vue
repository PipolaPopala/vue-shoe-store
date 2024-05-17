<script setup>
import DrawerHead from './DrawerHead.vue'
import CartItemList from './CartItemList.vue'

defineProps({
  totalPrice: Number
})
</script>

<template>
  <div class="fixed top-0 left-0 w-full h-full bg-black bg-opacity-50 z-10"></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
    <DrawerHead />
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
        disabled
        class="mt-4 bg-lime-500 w-full rounded-xl py-3 text-white disabled:bg-slate-300 hover:bg-lime-600 active:bg-lime-700 transition cursor-pointer"
      >
        Оформить заказ
      </button>
    </div>
  </div>
</template>
