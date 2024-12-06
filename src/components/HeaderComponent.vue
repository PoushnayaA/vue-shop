<template>
  <header
    class="flex justify-between items-center border-b border-slate-200 px-4 py-3 md:px-10 md:py-8"
  >
    <router-link to="/">
      <div class="flex items-center gap-4">
        <img src="/logo.png" alt="Logo" class="w-10" />
        <div>
          <h2 class="text-xl font-bold uppercase hidden md:block">
            Vue Sneakers
          </h2>
          <p class="text-slate-400 hidden md:block">Магазин лучших кроссовок</p>
        </div>
      </div>
    </router-link>
    <ul class="flex items-center gap-5 md:gap-10">
      <li
        @click="() => emit('openDrawer')"
        class="flex items-center gap-3 cursor-pointer text-gray-500 hover:text-black"
      >
        <img src="/cart.svg" alt="Корзина" />
        <b class="hidden md:block">{{ totalPrice }} руб.</b>
      </li>
      <router-link to="/favorites">
        <li
          class="flex items-center gap-3 cursor-pointer text-gray-500 hover:text-black"
        >
          <img src="/heart.svg" alt="Закладки" />
          <span class="hidden lg:block">Закладки</span>
        </li>
      </router-link>
      <li
        class="flex items-center gap-3 cursor-pointer text-gray-500 hover:text-black"
      >
        <img src="/profile.svg" alt="Профиль" />
        <span class="hidden lg:block">Профиль</span>
      </li>
    </ul>
  </header>
</template>

<script setup>
defineProps({
  totalPrice: Number,
})

const emit = defineEmits(['openDrawer'])
</script>

<style></style>
