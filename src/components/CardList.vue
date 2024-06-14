<script setup lang="ts">
import type {PropType} from "vue";

import Card from "./Card.vue";

type SneakersItem = {
  id: number,
  title: string,
  imageUrl: string,
  price: number,
  isFavorite: boolean,
  isAdded: boolean
}

defineProps({
  items: {
    type: Array as PropType<SneakersItem[]>,
    required: true
  },
})

const emit = defineEmits(['addToFavorite', 'addToCart']);
//const addToFavorite = inject('addToFavorite');
</script>

<template>
  <div class="grid grid-cols-4 gap-5" v-auto-animate>
   <Card
       v-for="item in items"
       :key="item.id"
       :id="item.id"
       :title="item.title"
       :imageUrl="item.imageUrl"
       :price="item.price"
       :onClickAdd="() => emit('addToCart', item)"
       :isAdded="item.isAdded"
       :onClickFavorite="() => emit('addToFavorite', item)"
       :isFavorite="item.isFavorite"
   />
  </div>
</template>

<style scoped>

</style>