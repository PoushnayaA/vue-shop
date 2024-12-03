<script setup>
import { ref, inject, computed } from 'vue'
import axios from 'axios'
import DrawerHead from './DrawerHead.vue'
import CartItemList from './CartItemList.vue'
import InfoBlock from './InfoBlock.vue'

const props = defineProps({
  totalPrice: Number,
  vatPrice: Number,
})

const isLoadingOrder = ref(false)
const orderId = ref(false)

const { cart } = inject('cart')

const createOrder = async () => {
  try {
    isLoadingOrder.value = true
    const { data } = await axios.post(
      `https://0bc60d73538f2561.mokky.dev/orders`,
      {
        items: cart.value,
        totalPrice: props.totalPrice.value,
      },
    )
    cart.value = []
    orderId.value = data.id
    return data
  } catch (err) {
    console.log(err)
  } finally {
    isLoadingOrder.value = false
  }
}
const isCartEmpty = computed(() => cart.value.length === 0)
const buttonDisabled = computed(() => isLoadingOrder.value || isCartEmpty.value)
</script>
<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
  <div
    v-auto-animate
    class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8 flex flex-col"
  >
    <DrawerHead />
    <div v-if="!totalPrice || orderId" class="flex h-full items-center">
      <InfoBlock
        v-if="!totalPrice && !orderId"
        title="Корзина пустая"
        description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ"
        image-url="/package-icon.png"
      />
      <InfoBlock
        v-if="orderId"
        title="Заказ оформлен!"
        :description="`Ваш заказ #${orderId} скоро будет передан курьерской доставке`"
        image-url="/order-success-icon.png"
      />
    </div>

    <div v-else class="flex flex-col h-full">
      <CartItemList />
      <div class="flex flex-col gap-4 mt-7">
        <div class="flex gap-2">
          <span>Итого:</span>
          <div class="flex-1 mb-1 border-b border-dashed"></div>
          <b>{{ totalPrice }} руб.</b>
        </div>
        <div class="flex gap-2">
          <span>Налог 5%:</span>
          <div class="flex-1 mb-1 border-b border-dashed"></div>
          <b>{{ vatPrice }} руб.</b>
        </div>
        <button
          @click="createOrder"
          :disabled="buttonDisabled"
          class="mt-4 bg-lime-500 w-full rounded-xl py-3 text-white disabled:bg-slate-300 hover:bg-lime-600 active:bg-lime-700 transition"
        >
          Оформить заказ
        </button>
      </div>
    </div>
  </div>
</template>
