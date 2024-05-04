<script setup>
import CartItem from './CartItem.vue'

const onClickAdd = () => {
  alert('Добавлено')
}
</script>

<template>
  <div class="flex flex-col gap-4">
    <CartItem />
    <CartItem />
    <CartItem />
    <CartItem />
    <CartItem />
  </div>
</template>
