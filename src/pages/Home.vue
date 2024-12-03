<script setup>
import axios from 'axios'
import CardList from '../components/CardList.vue'
import { inject, reactive, watch, ref, onMounted } from 'vue'

const { cart, addToCart, removeFromCart } = inject('cart')

const filters = reactive({
  sortBy: 'title',
  searchQuery: '',
})

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

    items.value = data.map(obj => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false,
    }))
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
// const fetchItems = async () => {
//   try {
//     const params = {
//       sortBy: filters.sortBy,
//     };

//     if (filters.searchQuery) {
//       params.title = `*${filters.searchQuery}*`;
//     }

//     const { data } = await axios.get(
//       `https://0bc60d73538f2561.mokky.dev/items`,
//       { params },
//     );

//     // Обновляем items с учетом избранных
//     items.value = data.map(obj => {
//       const existingItem = items.value.find(item => item.id === obj.id);
//       return {
//         ...obj,
//         isFavorite: existingItem ? existingItem.isFavorite : false,
//         favoriteId: existingItem ? existingItem.favoriteId : null,
//         isAdded: false,
//       };
//     });
//   } catch (err) {
//     console.log(err);
//   }
// };

watch(filters, fetchItems)
watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  { deep: true },
)

const onChangeSelect = event => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = event => {
  filters.searchQuery = event.target.value
}

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
  <div class="flex justify-between items-center">
    <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

    <div class="flex items-center gap-4">
      <select
        @change="onChangeSelect"
        class="py-2 px-3 border rounded-md outline-none"
      >
        <option value="name">По названию</option>
        <option value="price">Сначала дешевые</option>
        <option value="-price">Сначала дорогие</option>
      </select>
      <div class="relative">
        <img src="/search.svg" alt="Поиск" class="absolute top-3 left-4" />
        <input
          @input="onChangeSearchInput"
          placeholder="Поиск..."
          class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
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
