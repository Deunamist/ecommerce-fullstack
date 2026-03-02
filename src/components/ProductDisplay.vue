<template>
  <div :class="['page', pageClass]">

    <div class="background-top"></div>

    <!-- LOADING -->
    <div v-if="loading" class="card loading">
      <h2>Loading...</h2>
    </div>

    <!-- AVAILABLE PRODUCT -->
    <div v-else-if="product && !isUnavailable" class="card product-card">

      <!-- IMAGE -->
      <div class="product-image">
        <img :src="product.image" :alt="product.title" />
      </div>

      <!-- INFO -->
      <div class="product-info">

        <h1 class="product-title">{{ product.title }}</h1>

        <!-- CATEGORY + RATING -->
        <div class="category-rating">
          <span class="category">{{ formattedCategory }}</span>

          <div class="rating">
            <span class="rating-value">
              {{ (product.rating?.rate || 0).toFixed(1) }} / 5
            </span>

            <div class="rating-dots">
              <span
                v-for="n in 5"
                :key="n"
                :class="['dot', n <= Math.round(product.rating?.rate || 0) ? 'filled' : '']"
              ></span>
            </div>
          </div>
        </div>

        <p class="description">
          {{ product.description }}
        </p>

        <!-- PRICE + BUTTON SECTION -->
        <div class="price-section">
          <h2 class="price">${{ product.price }}</h2>

          <div class="buttons">
            <button class="btn-buy">Buy now</button>
            <button class="btn-next" @click="nextProduct">
              Next product
            </button>
          </div>
        </div>

      </div>
    </div>

    <!-- UNAVAILABLE PRODUCT -->
    <div v-else class="card unavailable-card unavailable-layout">
      <div class="sad-face-container">
        <img :src="sadFace" alt="Sad face" />
        <p class="unavailable-text">This product is unavailable to show</p>
        <button class="btn-unavailable-next" @click="nextProduct">
          Next product
        </button>
      </div>
    </div>

  </div>
</template>

<script>
import sadFace from '@/assets/sad-face.png'

export default {
  name: "ProductDisplay",

  data() {
    return {
      index: 1,
      product: null,
      loading: false,
      sadFace
    };
  },

  computed: {
    pageClass() {
      if (!this.product) return "page-unavailable";

      if (this.product.category === "men's clothing")
        return "page-men";

      if (this.product.category === "women's clothing")
        return "page-women";

      return "page-unavailable";
    },

    isUnavailable() {
      if (!this.product) return true;

      return !(
        this.product.category === "men's clothing" ||
        this.product.category === "women's clothing"
      );
    },

    formattedCategory() {
      if (!this.product) return "";

      if (this.product.category === "men's clothing")
        return "Men’s Clothing";

      if (this.product.category === "women's clothing")
        return "Women’s Clothing";

      return this.product.category;
    }
  },

  methods: {
    async fetchProduct() {
      this.loading = true;

      try {
        const response = await fetch(
          `https://fakestoreapi.com/products/${this.index}`
        );
        const data = await response.json();
        this.product = data;
      } catch (error) {
        console.error("Error fetching product:", error);
        this.product = null;
      }

      this.loading = false;
    },

    nextProduct() {
      this.index++;
      if (this.index > 20) this.index = 1;
      this.fetchProduct();
    }
  },

  mounted() {
    this.fetchProduct();
  }
};
</script>