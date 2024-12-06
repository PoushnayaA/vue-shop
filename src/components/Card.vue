<script setup>
import { computed } from 'vue'

defineProps({
  id: Number,
  imageUrl: String,
  title: String,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean,
  onClickFavorite: Function,
  onClickAdd: Function,
})
</script>

<template>
  <div
    class="relative flex flex-col bg-white border border-slate-100 rounded-3xl p-8 cursor-pointer transition hover:-translate-y-2 hover:shadow-xl"
  >
    <img
      v-if="onClickFavorite"
      @click="onClickFavorite"
      :src="!isFavorite ? '/like-1.svg' : '/like-2.svg'"
      alt="Like"
      class="absolute top-8 left-8"
    />
    <img
      :src="imageUrl"
      alt="Sneakers"
      class="ml-auto mr-auto h-[170px]"
      height="170"
    />
    <div class="flex flex-col justify-between h-full">
      <p class="mt-2">{{ title }}</p>

      <div class="flex justify-between">
        <div class="flex flex-col">
          <span class="text-slate-400 uppercase">Цена:</span>
          <b>{{ price }} руб.</b>
        </div>
        <img
          v-if="onClickAdd"
          @click="onClickAdd"
          :src="!isAdded ? '/plus.svg' : '/checked.svg'"
          alt="Plus"
        />
      </div>
    </div>
  </div>
</template>

<style></style>
