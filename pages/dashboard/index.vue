<template>
  <div class="dashboard">
    <Menu />
    <!-- Content -->
    <div class="content">
      <img src="/img/logo.png" alt="Logo" class="logo" />
      <div class="header">
        <h1>Dashboard Inventory</h1>
      </div>

      <!-- Stats Section -->
      <div class="stats">
        <nuxt-link to="/products"
          ><div class="stat-card">
            <h2>Total Product</h2>
            <p>{{ jmlah_product }}</p>
          </div></nuxt-link
        >
        <nuxt-link to="/peminjaman"
          ><div class="stat-card">
            <h2>Dipinjam</h2>
            <p>{{ jmlah_Dipinjam }}</p>
          </div></nuxt-link
        >
        <NuxtLink to="/pengembalian"
          ><div class="stat-card">
            <h2>DiKembalikan</h2>
            <p>{{ jmlah_kembali }}</p>
          </div></NuxtLink
        >
      </div>
    </div>
  </div>
</template>

<script setup>
definePageMeta({
  middleware: "auth",
});

const supabase = useSupabaseClient();
const jmlah_product = ref(0);
const jmlah_Dipinjam = ref(0);
const jmlah_kembali = ref(0);

async function getjmlah_product() {
  const { error, data, count } = await supabase
    .from("products")
    .select("*", { count: "exact" });
  if (count) jmlah_product.value = count;
}

async function getjmlah_Dipinjam() {
  const { error, data, count } = await supabase
    .from("peminjaman")
    .select("*", { count: "exact" });
  if (count) jmlah_Dipinjam.value = count;
}
async function getjmlah_kembali() {
  const { error, data, count } = await supabase
    .from("pengembalian")
    .select("*", { count: "exact" });
  if (count) jmlah_kembali.value = count;
}

onMounted(() => {
  getjmlah_product();
  getjmlah_Dipinjam();
  getjmlah_kembali();
});
</script>

<style scoped>
/* Reset default styles */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
a {
  text-decoration: none;
  color: white;
}
/* Layout */
.dashboard {
  display: flex;
  height: 100%;
}

/* Content */
.content {
  margin-left: 250px;
  padding: 30px;
  width: 100%;
  min-height: 100vh;
  overflow: hidden; /* Prevent overflow */
}

.header {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 30px;
}

.header h1 {
  font-size: 32px;
  font-weight: 600;
  color: #005696; /* Biru Tua */
}

/* Button */
.btn {
  background-color: #ffd700; /* Kuning */
  color: black;
  padding: 12px 24px;
  border-radius: 6px;
  font-size: 16px;
  cursor: pointer;
  font-weight: bold;
  transition: background-color 0.3s;
}

.btn:hover {
  background-color: #ffc107; /* Kuning Lebih Gelap */
}

/* Stats Section */
.stats {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 30px;
  margin-bottom: 30px;
}

.stat-card {
  background-color: #005696; /* Biru Tua */
  color: white;
  padding: 40px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.stat-card p {
  font-size: 24px;
}

/* Responsive design for smaller screens */
@media (max-width: 768px) {
  .stats {
    grid-template-columns: 1fr;
  }

  .content {
    margin-left: 0;
    padding: 20px;
  }

  .btn {
    padding: 10px 20px;
  }
}

/* Logo styling */
.logo {
  width: 250px;
  height: auto;
  display: block;
  margin: 20px auto;
}
</style>
