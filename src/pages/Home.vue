<script setup>
import VueSelect from 'vue3-select-component'
import axios from 'axios'
import debounce from 'lodash.debounce'
import CardList from '../components/CardList.vue'
import { inject, reactive, watch, ref, onMounted } from 'vue'

const { cart, addToCart, removeFromCart } = inject('cart')

const filters = reactive({
  sortBy: 'title',
  searchQuery: '',
})

const selectedOption = ref('')

const items = ref([])

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get(
      `https://0bc60d73538f2561.mokky.dev/favorites`,
    )

    items.value = items.value.map(item => {
      const favorite = favorites.find(favorite => favorite.item_id === item.id)

      if (!favorite) {
        return item
      }

      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id,
      }
    })
  } catch (err) {
    console.log(err)
  }
}

watch(cart, () => {
  items.value = items.value.map(item => ({
    ...item,
    isAdded: false,
  }))
})

//работает для пересортировки
const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy,
    }

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }

    const { data } = await axios.get(
      `https://0bc60d73538f2561.mokky.dev/items`,
      { params },
    )

    // Обновляем items с учетом избранных
    items.value = data.map(obj => {
      const existingItem = items.value.find(item => item.id === obj.id)
      return {
        ...obj,
        isFavorite: existingItem ? existingItem.isFavorite : false,
        favoriteId: existingItem ? existingItem.favoriteId : null,
        isAdded: false,
      }
    })
  } catch (err) {
    console.log(err)
  }
}

watch(filters, fetchItems)
watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  { deep: true },
)

const onChangeSelect = event => {
  filters.sortBy = selectedOption.value
}

watch(selectedOption, onChangeSelect)

const onChangeSearchInput = debounce(event => {
  filters.searchQuery = event.target.value
}, 200)

const addToFavorites = async item => {
  try {
    if (!item.isFavorite) {
      const obj = {
        item_id: item.id,
      }
      const { data } = await axios.post(
        `https://0bc60d73538f2561.mokky.dev/favorites`,
        obj,
      )
      item.isFavorite = true
      item.favoriteId = data.id
    } else {
      await axios.delete(
        `https://0bc60d73538f2561.mokky.dev/favorites/${item.favoriteId}`,
      )
      item.isFavorite = false
      item.favoriteId = null
    }
  } catch (err) {
    console.log(err)
  }
}

const onClickAddToCart = item => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []

  await fetchItems()
  await fetchFavorites()

  items.value = items.value.map(item => ({
    ...item,
    isAdded: cart.value.some(cartItem => cartItem.id === item.id),
  }))
})
</script>

<template>
  <div class="flex flex-col lg:flex-row justify-between lg:items-center">
    <h2 class="text-xl md:text-3xl font-bold mb-8 lg:mb-0">Все кроссовки</h2>
    <div class="flex flex-col md:flex-row md:items-center gap-4">
      <VueSelect
        class="custom-select"
        v-model="selectedOption"
        @change="onChangeSelect"
        :options="[
          { label: 'По названию', value: 'name' },
          { label: 'Сначала дешевые', value: 'price' },
          { label: 'Сначала дорогие', value: '-price' },
        ]"
        placeholder="Выберите сортировку"
      />

      <div class="relative w-50">
        <img src="/search.svg" alt="Поиск" class="absolute top-3 left-4" />
        <input
          @input="onChangeSearchInput"
          placeholder="Поиск..."
          class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400 w-full"
        />
      </div>
    </div>
  </div>
  <div class="mt-10">
    <CardList
      :items="items"
      @add-to-favorites="addToFavorites"
      @add-to-cart="onClickAddToCart"
    />
  </div>
</template>
<style scoped></style>
