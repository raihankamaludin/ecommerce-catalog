
<script setup>
import { ref, computed, onMounted } from 'vue'
import axios from 'axios'

const index = ref(1)
const product = ref(null)
const isLoading = ref(false) // Tambahkan state loading

const productClass = computed(() => {
  if (product.value?.category === "men's clothing") return 'page-men'
  if (product.value?.category === "women's clothing") return 'page-women'
  return 'page-unavailable'
})

const fetchProduct = async () => {
  isLoading.value = true // Tampilkan loader sebelum fetch
  try {
    const response = await axios.get(`https://fakestoreapi.com/products/${index.value}`)
    product.value = response.data || null
  } catch (error) {
    console.error('Error fetching product:', error)
    product.value = null
  } finally {
    isLoading.value = false // Sembunyikan loader setelah fetch selesai
  }
}

const nextProduct = () => {
  index.value = index.value < 20 ? index.value + 1 : 1
  fetchProduct()
}

onMounted(fetchProduct)

</script>

<template>
  <div :class="productClass" class="container">
    <div class="content">
    <!-- Loader -->
    <div v-if="isLoading" class="loader-container">
        <div class="loader"></div>
      </div>
      
      <!-- Page Unavailable Message -->
      <div v-if="productClass === 'page-unavailable'" class="unavailable-content">
        <p>This product is unavailable to show</p>
        <button class="next-button" @click="nextProduct">Next product</button>
      </div>

      <!-- Product Content (Hidden in page-unavailable) -->
      <div v-else class="product-content">
        <img v-if="product.image" :src="product.image" :alt="product.title" class="product-image" />
        <div class="product-details">
          <h2 class="product-title">{{ product.title }}</h2>

          <div class="category-rating">
            <p class="category">{{ product.category }}</p>
            <div class="rating">
              <span>{{ product.rating?.rate || 0 }}/5</span>
              <div class="dots">
                <div
                  v-for="n in 5"
                  :key="n"
                  :class="{ filled: n <= Math.round(product.rating?.rate || 0) }"
                ></div>
              </div>
            </div>
          </div>
          <hr />
          <div class="description">
            <p>{{ product.description }}</p>
          </div>
          <hr />
          <div class="price">
            <h2>${{ product.price }}</h2>
          </div>
          <div class="buttons">
            <button class="buy-button">Buy now</button>
            <button class="next-button" @click="nextProduct">Next product</button>
          </div>
        </div>
      </div>
      
    </div>
  </div>
</template>

