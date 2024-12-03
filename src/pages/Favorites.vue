<script setup>
import CardList from '../components/CardList.vue'
import { onMounted, ref, inject } from 'vue'
import axios from 'axios'

const favorites = ref([])

onMounted(async () => {
  try {
    const { data } = await axios.get(
      'https://0bc60d73538f2561.mokky.dev/favorites?_relations=items',
    )
    favorites.value = data.map(obj => obj.item)
  } catch (err) {
    console.log(err)
  }
})
</script>
<template>
  <div class="flex justify-between items-center">
    <h2 class="text-3xl font-bold mb-8">Мои закладки</h2>
  </div>
  <div class="mt-10">
    <CardList :items="favorites" isFavoritesPage="true" />
  </div>
</template>
