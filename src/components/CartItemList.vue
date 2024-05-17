<script setup>
import { inject } from 'vue'
import CartItem from './CartItem.vue'

const { cart, addToCart } = inject('cart')
</script>

<template>
  <div class="flex flex-col gap-4">
    <CartItem
      v-for="item in cart"
      :key="item.id"
      :title="item.title"
      :price="item.price"
      :image-url="item.imageUrl"
      @add-to-cart="() => addToCart(item)"
    />
  </div>
</template>
