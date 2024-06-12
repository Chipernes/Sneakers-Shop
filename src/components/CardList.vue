<script setup lang="ts">

import Card from "./Card.vue";
import type {PropType} from "vue";

type SneakersItems = {
  id: number,
  title: string,
  imageUrl: string,
  price: number
}

defineProps({
  items: {
    type: Array as PropType<SneakersItems[]>,
    required: true
  },
})

const onClickAdd = () => {
  alert('Add')
}
</script>

<template>
  <div class="grid grid-cols-4 gap-5 ">
   <Card
       v-for="item in items"
       :key="item.id"
       :title="item.title"
       :imageUrl="item.imageUrl"
       :price="item.price"
       :onClickAdd="onClickAdd"
   />
  </div>
</template>

<style scoped>

</style>