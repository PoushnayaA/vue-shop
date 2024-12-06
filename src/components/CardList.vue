<script setup>
import Card from './Card.vue'

defineProps({
  items: Array,
  isFavoritesPage: Boolean,
})

const emit = defineEmits(['addToFavorites', 'addToCart'])
</script>

<template>
  <div
    v-auto-animate
    class="grid grid-cols-1 md:grid-cols-3 lg:grid-cols-4 gap-5"
  >
    <Card
      v-for="item in items"
      :key="item.id"
      :id="item.id"
      :image-url="item.imageUrl"
      :title="item.title"
      :price="item.price"
      :on-click-favorite="
        !isFavoritesPage ? () => emit('addToFavorites', item) : null
      "
      :on-click-add="!isFavoritesPage ? () => emit('addToCart', item) : null"
      :is-favorite="item.isFavorite"
      :is-added="item.isAdded"
    />
  </div>
</template>

<style></style>
