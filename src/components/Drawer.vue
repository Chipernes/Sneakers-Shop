<script setup lang="ts">
import axios from 'axios'
import { computed, ref, inject } from 'vue'
import type { Ref } from 'vue'

import DrawerHead from './DrawerHead.vue'
import CartItemList from './CartItemList.vue'
import InfoBlock from '@/components/InfoBlock.vue'

type SneakersItem = {
  id: number
  title: string
  imageUrl: string
  price: number
  isFavorite: boolean
  favoriteId: number
  isAdded: boolean
}

type Cart = {
  cart: Ref<Array<SneakersItem>>
}

const isCreating = ref(false)
const orderId = ref(null)

const props = defineProps({
  totalPrice: Number
})

const cartActions = inject<Cart>('cart')

if (!cartActions) {
  throw new Error('cartActions injection failed')
}

const { cart } = cartActions

const createOrder = async () => {
  try {
    isCreating.value = true
    const { data } = await axios.post('https://058e50087728bbd6.mokky.dev/orders', {
      items: cart.value,
      totalPrice: props.totalPrice
    })

    cart.value = []

    orderId.value = data.id

    return data
  } catch (err) {
    console.log(err)
  } finally {
    isCreating.value = false
  }
}

const cartIsEmpty = computed(() => cart.value.length === 0)
const buttonDisabled = computed(() => isCreating.value || cartIsEmpty.value)
</script>

<template>
  <div class="fixed top-0 left-0 w-full h-full bg-black z-10 opacity-70"></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
    <DrawerHead />

    <div v-if="!totalPrice || orderId" class="flex h-full items-center">
      <InfoBlock
        v-if="!totalPrice && !orderId"
        title="Cart is empty"
        description="Add at least one pair of sneakers to place your order."
        image-url="/package-icon.png"
      />

      <InfoBlock
        v-if="orderId"
        title="Order is processed!"
        :description="`Your order № ${orderId}. A manager will contact you soon.`"
        image-url="/order-success-icon.png"
      />
    </div>

    <div v-else>
      <CartItemList />

      <div class="flex flex-col mt-7">
        <div class="flex gap-2">
          <span>Total:</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>${{ totalPrice }}</b>
        </div>

        <button
          :disabled="buttonDisabled"
          @click="createOrder"
          class="mt-4 bg-lime-500 w-full rounded-xl py-3 text-white disabled:bg-slate-300 hover:bg-lime-600 transition active:bg-lime-700"
        >
          Create order
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
