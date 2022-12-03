<script setup>
import { ref, watch } from 'vue'
import { debounce } from 'debounce'

const isLoading = ref(false)
const searchTerm = ref('')
const searchResults = ref({
  limit: 0,
  products: [],
  skip: 0,
  total: 0,
})
const products = ref([])

const findProducts = debounce(async term => {
  try {
    if (searchTerm.length === 0) return (prodcuts.value = [])

    isLoading.value = true
    const baseURL = `https://dummyjson.com/products`
    const response = await fetch(`${baseURL}/search?q=${searchTerm.value}`)
    const responseJSON = await response.json()
    if (responseJSON.products) {
      products.value = responseJSON.products
    } else {
      products.value = []
    }
  } catch (error) {
    console.log(error)
    alert('Something went wrong!')
  } finally {
    isLoading.value = false
  }
}, 300)

watch(searchTerm, newSearchTerm => findProducts(newSearchTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <div v-if="isLoading">Please wait...</div>
    <div v-else-if="searchTerm && products">
      <ul class="list-disc">
        <li v-for="product in products">{{ product.title }} - ${{ product.price }}</li>
      </ul>
    </div>
  </div>
</template>
