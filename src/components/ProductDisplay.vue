<template>
  <div :class="['product-container', pageClass]">
    <div v-if="isLoading" class="loading-container" aria-live="polite">
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

    <div v-else-if="!showUnavailable" class="product-card">
      <div class="product-image">
        <img :src="currentProduct.image" :alt="currentProduct.title" />
      </div>

      <div class="product-details">
        <div class="header-row">
          <div class="header-left">
            <div class="title-row">
              <div class="toggle" role="tablist" aria-label="Category toggle">
                <button
                  class="toggle-btn"
                  :class="{ active: selectedCategory === `men's clothing` }"
                  type="button"
                  @click="setCategory(`men's clothing`)"
                  :disabled="isLoading"
                >
                  Men
                </button>
                <button
                  class="toggle-btn"
                  :class="{ active: selectedCategory === `women's clothing` }"
                  type="button"
                  @click="setCategory(`women's clothing`)"
                  :disabled="isLoading"
                >
                  Women
                </button>
              </div>

              <h2 class="product-title">{{ currentProduct.title }}</h2>
            </div>

            <p class="product-category">{{ currentProduct.category }}</p>
          </div>

          <div class="header-right" aria-label="Product rating">
            <span class="rating-number">{{ ratingValue.toFixed(1) }}/5</span>

            <div class="rating" aria-hidden="true">
              <span v-for="star in 5" :key="star" class="star">
                {{ star <= roundedRating ? "★" : "☆" }}
              </span>
            </div>

            <span class="rating-count">({{ ratingCount }})</span>

            <button
              class="mini-action btn-unavailable"
              type="button"
              @click="openUnavailable"
              :disabled="isLoading"
            >
              View Reviews
            </button>
          </div>
        </div>

        <p class="product-description">{{ currentProduct.description }}</p>

        <div class="bottom-row">
          <p class="product-price">${{ priceText }}</p>

          <div class="button-group">
            <button
              :class="['btn', 'btn-buy', btnBuyClass]"
              :disabled="isLoading"
              type="button"
              @click="openBuyNow"
            >
              Buy now
            </button>

            <button
              class="btn btn-next"
              @click="nextProduct"
              :disabled="isLoading"
              :aria-busy="isLoading"
              type="button"
            >
              {{ isLoading ? "Loading..." : "Next Product" }}
            </button>
          </div>
        </div>
      </div>
    </div>

    <div v-else class="unavailable-message">
      <div class="sad-face" aria-hidden="true">
        <div class="sad-brow"></div>
        <div class="sad-brow right"></div>
        <div class="sad-eye"></div>
        <div class="sad-eye right"></div>
        <div class="sad-mouth"></div>
      </div>

      <p class="unavailable-text">This page unavailable right now</p>

      <button class="btn btn-next" @click="backToProduct" :disabled="isLoading" type="button">
        Back to Product
      </button>
    </div>

    <div
      v-if="showBuyModal"
      class="modal-overlay"
      role="dialog"
      aria-modal="true"
      aria-label="Buy now checkout"
      @click.self="closeBuyNow"
    >
      <div class="modal-card" ref="modalCard" tabindex="-1">
        <div class="modal-header">
          <div class="modal-title">
            Checkout
            <span class="modal-subtitle">Review your item</span>
          </div>

          <button class="icon-btn" type="button" @click="closeBuyNow" aria-label="Close modal">
            ✕
          </button>
        </div>

        <div class="modal-body">
          <div class="modal-product">
            <div class="modal-thumb">
              <img :src="currentProduct.image" :alt="currentProduct.title" />
            </div>

            <div class="modal-info">
              <div class="modal-product-title">{{ currentProduct.title }}</div>
              <div class="modal-meta">
                <span class="pill">{{ currentProduct.category }}</span>
                <span class="pill">{{ ratingValue.toFixed(1) }}/5</span>
                <span class="pill">{{ ratingCount }} reviews</span>
              </div>
            </div>
          </div>

          <div class="modal-summary">
            <div class="summary-row">
              <span>Price</span>
              <strong>${{ priceText }}</strong>
            </div>
            <div class="summary-row">
              <span>Shipping</span>
              <strong>Free</strong>
            </div>
            <div class="summary-divider"></div>
            <div class="summary-row total">
              <span>Total</span>
              <strong>${{ priceText }}</strong>
            </div>
          </div>
        </div>

        <div class="modal-actions">
          <button class="btn btn-next modal-btn" type="button" @click="closeBuyNow">
            Continue browsing
          </button>

          <button
            :class="['btn', 'btn-buy', btnBuyClass, 'modal-btn']"
            type="button"
            @click="fakeCheckout"
          >
            Checkout
          </button>
        </div>
      </div>
    </div>

    <div v-if="toast.show" class="toast" role="status" aria-live="polite">
      {{ toast.message }}
    </div>
  </div>
</template>

<script>
export default {
  name: "ProductDisplay",
  data() {
    return {
      currentProduct: {},
      productIndex: 1,
      isLoading: false,
      showUnavailable: false,
      validCategories: ["men's clothing", "women's clothing"],

      selectedCategory: "men's clothing",

      showBuyModal: false,
      toast: { show: false, message: "" },
      toastTimer: null,

      lastValidProduct: null,
      lastValidIndex: 1,
    };
  },
  computed: {
    pageClass() {
      if (this.showUnavailable) return "page-unavailable";
      if (this.currentProduct?.category === "men's clothing") return "page-men";
      if (this.currentProduct?.category === "women's clothing") return "page-women";
      return this.selectedCategory === "women's clothing" ? "page-women" : "page-men";
    },
    btnBuyClass() {
      if (this.currentProduct?.category === "women's clothing") return "btn-buy-women";
      if (this.currentProduct?.category === "men's clothing") return "btn-buy-men";
      return this.selectedCategory === "women's clothing" ? "btn-buy-women" : "btn-buy-men";
    },
    ratingValue() {
      return Number(this.currentProduct?.rating?.rate || 0);
    },
    roundedRating() {
      return Math.round(this.ratingValue);
    },
    ratingCount() {
      return Number(this.currentProduct?.rating?.count || 0);
    },
    priceText() {
      return Number(this.currentProduct?.price || 0).toFixed(2);
    },
  },
  methods: {
    async findNextMatchingProduct() {
      if (this.isLoading) return;

      this.isLoading = true;
      this.showUnavailable = false;

      try {
        let tries = 0;

        while (tries < 20) {
          const res = await fetch(`https://fakestoreapi.com/products/${this.productIndex}`);
          const data = await res.json();

          const isValid = this.validCategories.includes(data.category);
          const matchToggle = data.category === this.selectedCategory;

          if (isValid && matchToggle) {
            this.currentProduct = { ...data };

            this.lastValidProduct = { ...data };
            this.lastValidIndex = this.productIndex;

            this.showUnavailable = false;
            return;
          }

          this.productIndex++;
          if (this.productIndex > 20) this.productIndex = 1;
          tries++;
        }

        this.currentProduct = {};
        this.showUnavailable = true;
      } catch (e) {
        console.error(e);
        this.currentProduct = {};
        this.showUnavailable = true;
      } finally {
        this.isLoading = false;
      }
    },

    nextProduct() {
      if (this.isLoading) return;
      this.productIndex++;
      if (this.productIndex > 20) this.productIndex = 1;
      this.findNextMatchingProduct();
    },

    setCategory(category) {
      if (this.isLoading) return;
      this.selectedCategory = category;
      this.findNextMatchingProduct();
    },

    openUnavailable() {
      if (this.isLoading) return;

      if (this.currentProduct && this.currentProduct.id) {
        this.lastValidProduct = { ...this.currentProduct };
        this.lastValidIndex = this.productIndex;
      }

      this.showUnavailable = true;
    },

    backToProduct() {
      if (this.isLoading) return;

      this.showUnavailable = false;

      if (this.lastValidProduct && this.lastValidProduct.id) {
        this.currentProduct = { ...this.lastValidProduct };
        this.productIndex = this.lastValidIndex || 1;
        return;
      }

      this.productIndex = 1;
      this.findNextMatchingProduct();
    },

    openBuyNow() {
      if (this.isLoading || this.showUnavailable) return;

      this.showBuyModal = true;
      document.body.classList.add("no-scroll");

      this.$nextTick(() => {
        const el = this.$refs.modalCard;
        if (el && el.focus) el.focus();
      });

      window.addEventListener("keydown", this.onKeydown);
    },

    closeBuyNow() {
      this.showBuyModal = false;
      document.body.classList.remove("no-scroll");
      window.removeEventListener("keydown", this.onKeydown);
    },

    onKeydown(e) {
      if (e.key === "Escape") this.closeBuyNow();
    },

    fakeCheckout() {
      this.closeBuyNow();
      this.showToast("Checkout success. Great choice! (dummy)");
    },

    showToast(message) {
      this.toast.message = message;
      this.toast.show = true;

      if (this.toastTimer) clearTimeout(this.toastTimer);
      this.toastTimer = setTimeout(() => {
        this.toast.show = false;
      }, 2200);
    },
  },
  mounted() {
    this.findNextMatchingProduct();
  },
  beforeUnmount() {
    window.removeEventListener("keydown", this.onKeydown);
    if (this.toastTimer) clearTimeout(this.toastTimer);
  },
};
</script>