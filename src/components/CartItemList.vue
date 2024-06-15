<script setup lang="ts">
import { inject } from 'vue'
import CartItem from './CartItem.vue'

type SneakersItem = {
  id: number
  title: string
  imageUrl: string
  price: number
}

type Cart = {
  cart: SneakersItem[]
  removeFromCart: (param: SneakersItem) => void
}

const cartItem = inject<Cart>('cart')

if (!cartItem) {
  throw new Error('cartActions injection failed')
}
const { cart, removeFromCart } = cartItem
</script>

<template>
  <div class="flex flex-col gap-4" v-auto-animate>
    <CartItem
      v-for="item in cart"
      :key="item.id"
      :title="item.title"
      :price="item.price"
      :image-url="item.imageUrl"
      @on-click-remove="() => removeFromCart(item)"
    />
  </div>
</template>

<style scoped></style>
