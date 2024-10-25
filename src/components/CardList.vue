<script setup>
import Card from './Card.vue'

const onClickAdd = () => {
  alert('add')
}
</script>

<template>
  <div class="grid grid-cols-4 gap-5">
    <Card
      image-url="/sneakers/sneakers-3.jpg"
      :title="343"
      :price="43930"
      :is-added="true"
      :is-favorite="true"
      :on-click-add="onClickAdd"
    />
  </div>
</template>

<style></style>
