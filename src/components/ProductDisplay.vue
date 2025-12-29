<template>
  <div class="page" :class="pageClass">
    <div class="card">
      <div v-if="loading" class="loader-wrap">
        <div class="loader"></div>
      </div>

      <template v-else>
        <div v-if="isMenOrWomen" class="card-grid">
          <div class="img-wrap">
            <img :src="product.image" :alt="product.title" />
          </div>

          <div class="content">
            <h1 class="title">{{ product.title }}</h1>

            <div class="meta">
              <span class="category">{{ product.category }}</span>
              <span class="dot">•</span>
              <span class="rating">
                {{ product.rating?.rate ?? "-" }}/5
                <span class="small">({{ product.rating?.count ?? 0 }})</span>
              </span>
            </div>

            <p class="desc">{{ product.description }}</p>

            <div class="price">${{ Number(product.price).toFixed(2) }}</div>

            <div class="actions">
              <button class="btn primary" type="button">Buy now</button>
              <button class="btn outline" type="button" @click="nextProduct">
                Next product
              </button>
            </div>
          </div>
        </div>

        <div v-else class="unavailable">
          <div class="unavailable-illus" aria-hidden="true"></div>
          <div class="unavailable-box">
            <p class="unavailable-text">This product is unavailable to show</p>
            <button class="btn outline dark" type="button" @click="nextProduct">
              Next product
            </button>
          </div>
        </div>
      </template>
    </div>
  </div>
</template>

<script>
export default {
  name: "ProductDisplay",
  data() {
    return {
      index: 1,
      loading: false,
      product: null,
    };
  },
  computed: {
    category() {
      return (this.product?.category || "").toLowerCase();
    },
    isMen() {
      return this.category === "men's clothing";
    },
    isWomen() {
      return this.category === "women's clothing";
    },
    isMenOrWomen() {
      return this.isMen || this.isWomen;
    },
    pageClass() {
      if (this.loading) return "theme-neutral";
      if (this.isMen) return "theme-men";
      if (this.isWomen) return "theme-women";
      return "theme-unavailable";
    },
  },
  methods: {
    async fetchProduct() {
      this.loading = true;
      this.product = null;

      try {
        const res = await fetch(
          `https://fakestoreapi.com/products/${this.index}`
        );
        if (!res.ok) throw new Error("Failed to fetch product");
        this.product = await res.json();
      } catch (e) {
        this.product = { category: "unavailable" };
      } finally {
        this.loading = false;
      }
    },
    nextProduct() {
      this.index += 1;
      if (this.index > 20) this.index = 1;
      this.fetchProduct();
    },
  },
  mounted() {
    this.fetchProduct();
  },
};
</script>
