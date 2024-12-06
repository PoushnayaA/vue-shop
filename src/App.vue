<script setup>
import { provide, ref, watch, computed } from 'vue'

import HeaderComponent from './components/HeaderComponent.vue'
import Drawer from './components/Drawer.vue'

/* Корзина */
const cart = ref([])
const isVisibleDrawer = ref(false)

const totalPrice = computed(() =>
  cart.value.reduce((acc, item) => acc + item.price, 0),
)
const vatPrice = computed(() => Math.round(totalPrice.value * 0.05))

const closeDrawer = () => {
  isVisibleDrawer.value = false
}

const openDrawer = () => {
  isVisibleDrawer.value = true
}

const addToCart = item => {
  cart.value.push(item)
  item.isAdded = true
}

const removeFromCart = item => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}

watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  { deep: true },
)

provide('cart', {
  cart,
  closeDrawer,
  openDrawer,
  addToCart,
  removeFromCart,
})

/* Конец корзины */
</script>

<template>
  <Drawer
    v-if="isVisibleDrawer"
    :total-price="totalPrice"
    :vat-price="vatPrice"
  />
  <div
    class="bg-white w-11/12 md:w-4/5 m-auto rounded-xl shadow-xl mt-8 mb-8 md:mt-10 md:mb-10"
  >
    <HeaderComponent :total-price="totalPrice" @open-drawer="openDrawer" />

    <div class="px-4 py-6 md:p-10">
      <Home />
      <router-view></router-view>
    </div>
  </div>
</template>

<style scoped></style>
