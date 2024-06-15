<script setup lang="ts">
import {computed, provide, ref, watch} from "vue";
import type {Ref} from "vue";
import axios from "axios";

import Header from "@/components/Header.vue";
import Drawer from "@/components/Drawer.vue";

type SneakersItem = {
  id: number,
  title: string,
  imageUrl: string,
  price: number,
  isFavorite: boolean,
  favoriteId: number,
  isAdded: boolean
}

const cart: Ref<Array<SneakersItem>> = ref([]);
const isCreatingOrder = ref(false);

const drawerOpen = ref(false);

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0));

const cartIsEmpty = computed(() => cart.value.length === 0);
const cartButtonDisabled = computed(() => isCreatingOrder.value || cartIsEmpty.value);

const closeDrawer = () => {
  drawerOpen.value = false;
};

const openDrawer = () => {
  drawerOpen.value = true;
};

const addToCart = (item: SneakersItem) => {
  cart.value.push(item);
  item.isAdded = true;
};

const removeFromCart = (item: SneakersItem) => {
  cart.value.splice(cart.value.indexOf(item), 1);
  item.isAdded = false;
};

const createOrder = async () => {
  try {
    isCreatingOrder.value = true;
    const {data} = await axios.post('https://058e50087728bbd6.mokky.dev/orders', {
      items: cart.value,
      totalPrice: totalPrice.value,
    });

    cart.value = [];

    return data;
  } catch (err) {
    console.log(err);
  } finally {
    isCreatingOrder.value = false;
  }
};

watch(cart, () => {
      localStorage.setItem('cart', JSON.stringify(cart.value));
    },
    {
      deep: true
    }
);

provide('cart', {
  cart,
  closeDrawer,
  openDrawer,
  addToCart,
  removeFromCart
})
</script>

<template>
  <Drawer
      v-if="drawerOpen"
      :total-price="totalPrice"
      @create-order="createOrder"
      :button-disabled="cartButtonDisabled"
  />

  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Header :total-price="totalPrice" @open-drawer="openDrawer"/>

    <div class="p-10">
      <RouterView/>
    </div>
  </div>
</template>

<style scoped>

</style>
