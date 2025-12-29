# Ecommerce Product Display (Vue + FakeStoreAPI)

Mini aplikasi ecommerce berbasis **Vue** yang menampilkan produk dari **FakeStoreAPI** dengan fitur filter kategori **Men / Women**, navigasi **Next Product**, **Buy Now (modal checkout)**, serta halaman **Unavailable**.

## Fitur Utama

- Menampilkan data produk dari **FakeStoreAPI**
- Toggle kategori:
  - `men's clothing`
  - `women's clothing`
- Tombol **Next Product** untuk berpindah ke produk berikutnya sesuai kategori
- **Buy Now Modal** (simulasi checkout)
- Halaman **Unavailable** dengan teks:
  > *This page unavailable right now*
- Tombol **Back to Product** untuk kembali ke halaman produk
- **Skeleton loading** saat data sedang diambil
- Tampilan **responsif** (desktop & mobile)

## Teknologi yang Digunakan

- **Vue.js**
- **FakeStoreAPI**
- **CSS custom** 

## API yang Digunakan

- Base URL:  
  `https://fakestoreapi.com/`
- Ambil produk berdasarkan ID:  
  `https://fakestoreapi.com/products/{id}`
- Rentang ID produk: `1 – 20`

## Cara Kerja Aplikasi

### Filter Kategori
Aplikasi **hanya menampilkan** produk dengan kategori:
- `men's clothing`
- `women's clothing`

Jika produk yang diambil **tidak sesuai** dengan kategori yang dipilih, aplikasi akan otomatis mencari produk berikutnya hingga menemukan yang valid.

### Halaman Unavailable
- Tombol **View Unavailable** menampilkan halaman simulasi produk tidak tersedia
- Tombol **Back to Product** akan:
  - Kembali ke produk terakhir yang valid
  - Tanpa reload halaman

### Buy Now
- Menampilkan modal checkout
- Bersifat **demo / simulasi** (tidak ada pembayaran asli)

