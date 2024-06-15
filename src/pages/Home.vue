<script setup lang="ts">
import axios from "axios";
import {inject, reactive, watch, onMounted, ref, Ref} from "vue";

import CardList from "../components/CardList.vue";

type SneakersItem = {
  id: number,
  title: string,
  imageUrl: string,
  price: number,
  isFavorite: boolean,
  favoriteId: number,
  isAdded: boolean
}

type Cart = {
  cart: Ref<Array<SneakersItem>>,
  addToCart: (param) => void,
  removeFromCart: (param) => void,
}

const {cart, addToCart, removeFromCart} = inject<Cart>('cart');

const items: Ref<Array<SneakersItem>> = ref([]);

const filters = reactive({
  sortBy: 'id',
  searchQuery: ''
});

const onClickAddPlus = (item: SneakersItem) => {
  if (!item.isAdded) {
    addToCart(item);
  } else {
    removeFromCart(item);
  }
};

const onChangeSelect = (event: any) => {
  filters.sortBy = event.target.value;
};

const onChangeSearchInput = (event: any) => {
  filters.searchQuery = event.target.value;
};

const addToFavorite = async (item: SneakersItem) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id
      };

      item.isFavorite = true;

      const {data} = await axios.post('https://058e50087728bbd6.mokky.dev/favorites', obj);

      item.favoriteId = data.id;
    } else {
      item.isFavorite = false;
      await axios.delete(`https://058e50087728bbd6.mokky.dev/favorites/${item.favoriteId}`);
      item.favoriteId = 0;
    }
  } catch (err) {
    console.log(err);
  }
};

const fetchFavorites = async () => {
  try {
    const {data: favorites} = await axios.get('https://058e50087728bbd6.mokky.dev/favorites');

    type FavoriteItem = {
      id: number,
      parentId: number
    }

    items.value = items.value.map(item => {
      const favorite = favorites.find((favorite: FavoriteItem) => favorite.parentId === item.id);

      if (!favorite) {
        return item;
      }

      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      }
    });
  } catch (err) {
    console.log(err);
  }
};

const fetchItems = async () => {
  try {
    const params = {
      title: '*',
      sortBy: filters.sortBy,
    };

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`;
    }

    const {data} = await axios.get('https://058e50087728bbd6.mokky.dev/items', {
      params
    });
    items.value = data.map((obj: SneakersItem) => ({
      ...obj,
      isFavorite: false,
      favoriteId: 0,
      isAdded: false
    }));
  } catch (err) {
    console.log(err);
  }
};

onMounted(async () => {
  const localCart = localStorage.getItem('cart');
  cart.value = localCart ? JSON.parse(localCart) : [];

  await fetchItems();
  await fetchFavorites();

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }));
});

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
});

watch(filters, fetchItems);

</script>

<template>
  <div class="flex justify-between items-center">
    <h2 class="text-3xl font-bold mb-8">All sneakers</h2>

    <div class="flex gap-4">
      <select @change="onChangeSelect" class="py-2 px-3 border rounded outline-none">
        <option value="">By rating</option>
        <option value="title">By name</option>
        <option value="price">By price (cheap)</option>
        <option value="-price">By price (expensive)</option>
      </select>

      <div class="relative">
        <img class="absolute left-4 top-3" src="/search.svg" alt="Search"/>
        <input
            @input="onChangeSearchInput"
            class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
            placeholder="Search..."
        >
      </div>
    </div>
  </div>

  <div class="mt-10">
    <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus"/>
  </div>
</template>

<style scoped>

</style>