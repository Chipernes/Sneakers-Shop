<script setup lang="ts">

import {inject} from "vue";
import CartItem from "./CartItem.vue";

type SneakersItem = {
  id: number,
  title: string,
  imageUrl: string,
  price: number,
}

type Cart = {
  cart: SneakersItem[];
}

const cartItem = inject<Cart>('cart');

if (!cartItem) {
  throw new Error('cartActions injection failed');
}
const { cart } = cartItem;
</script>

<template>
  <div class="flex flex-col gap-4">
    <CartItem v-for="item in cart" :key="item.id" :title="item.title" :price="item.price" :image-url="item.imageUrl"/>
  </div>
</template>

<style scoped>

</style>