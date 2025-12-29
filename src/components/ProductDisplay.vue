<template>
  <div :class="['product-container', pageClass]">
    <!-- Loading State -->
    <div v-if="isLoading" class="loading-container">
      <div class="skeleton-card">
        <div class="skeleton-image"></div>
        <div class="skeleton-content">
          <div class="skeleton-title"></div>
          <div class="skeleton-text"></div>
          <div class="skeleton-text"></div>
          <div class="skeleton-price"></div>
          <div class="skeleton-buttons">
            <div class="skeleton-button"></div>
            <div class="skeleton-button"></div>
          </div>
        </div>
      </div>
    </div>

    <!-- Product Card -->
    <div v-else class="product-card">
      <div class="product-image">
        <img :src="currentProduct.image" :alt="currentProduct.title" />
      </div>
      
      <div class="product-details">
        <h2 class="product-title">{{ currentProduct.title }}</h2>
        <p class="product-category">{{ currentProduct.category }}</p>
        
        <div class="rating">
          <span v-for="star in 5" :key="star" class="star">
            {{ star <= Math.round(currentProduct.rating?.rate || 0) ? '●' : '○' }}
          </span>
        </div>
        
        <p class="product-description">{{ currentProduct.description }}</p>
        
        <p class="product-price">${{ currentProduct.price }}</p>
        
        <div class="button-group">
          <button :class="['btn', 'btn-buy', btnBuyClass]">Buy now</button>
          <button class="btn btn-next" @click="nextProduct">Next product</button>
        </div>
      </div>
    </div>

    <!-- Unavailable Product Message -->
    <div v-if="showUnavailable" class="unavailable-message">
      <p class="unavailable-text">This product is unavailable to show</p>
      <button class="btn btn-next" @click="nextProduct">Next product</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductDisplay',
  data() {
    return {
      currentProduct: {},
      productIndex: 1,
      isLoading: false,
      showUnavailable: false,
      validCategories: ["men's clothing", "women's clothing"]
    };
  },
  computed: {
    pageClass() {
      if (this.showUnavailable) return 'page-unavailable';
      if (this.currentProduct.category === "men's clothing") return 'page-men';
      if (this.currentProduct.category === "women's clothing") return 'page-women';
      return '';
    },
    btnBuyClass() {
      if (this.currentProduct.category === "men's clothing") return 'btn-buy-men';
      if (this.currentProduct.category === "women's clothing") return 'btn-buy-women';
      return '';
    }
  },
  methods: {
    async fetchProduct() {
      this.isLoading = true;
      this.showUnavailable = false;
      
      try {
        const response = await fetch(`https://fakestoreapi.com/products/${this.productIndex}`);
        const data = await response.json();
        
        if (this.validCategories.includes(data.category)) {
          this.currentProduct = data;
          this.showUnavailable = false;
        } else {
          this.currentProduct = {};
          this.showUnavailable = true;
        }
      } catch (error) {
        console.error('Error fetching product:', error);
        this.showUnavailable = true;
      } finally {
        this.isLoading = false;
      }
    },
    nextProduct() {
      this.productIndex++;
      if (this.productIndex > 20) {
        this.productIndex = 1;
      }
      this.fetchProduct();
    }
  },
  mounted() {
    this.fetchProduct();
  }
};
</script>

<style scoped>
/* Styles will be in page.css */
</style>