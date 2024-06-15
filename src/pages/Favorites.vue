<script setup lang="ts">
import { onMounted, ref } from 'vue'
import axios from 'axios'

import CardList from '../components/CardList.vue'

const favorites = ref([])

onMounted(async () => {
  try {
    const { data } = await axios.get(
      'https://058e50087728bbd6.mokky.dev/favorites?_relations=items'
    )

    type JoinedItems = {
      id: number
      item: object
    }

    favorites.value = data.map((obj: JoinedItems) => obj.item)
  } catch (err) {
    console.log(err)
  }
})
</script>

<template>
  <h2 class="test text-3xl font-bold mb-8">My favorites</h2>

  <div class="mt-10">
    <CardList :items="favorites" is-favorites />
  </div>
</template>

<style scoped></style>
