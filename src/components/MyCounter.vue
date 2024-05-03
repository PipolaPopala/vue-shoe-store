<script setup>
// export default {
//   data() {
//     return {
//       count: 0
//     }
//   },
//   methods: {
//     increment() {
//       this.count++
//     }
//   }
// }

import { ref } from 'vue'

let count = ref(0)

function increment() {
  count.value++
}
</script>

<template>
  <h1>{{ count }}</h1>
  <button @click="increment">+</button>
</template>

<style scoped></style>
