<template>
  <div class="dashboard">
    <Menu />

    <div class="content">
      <div class="content-main">
        <img src="/img/logo.png" alt="Logo" class="logo" />
        <div class="header">
          <h1>Daftar Produk</h1>
          <router-link to="/alat" class="btn">Tambah Produk</router-link>
        </div>

        <div class="my-3">
          <form @submit.prevent="getData">
            <input v-model="keyword" type="search" class="form-control rounded-5" placeholder="Cari nama produk...">
          </form>
        </div>

        <div class="my-3 text-muted">menampilkan {{ data?.length }} dari {{ Totalproducts }}</div>

        <div class="table-container">
          <table>
            <thead>
              <tr>
                <th>NO.</th>
                <th>Tanggal</th>
                <th>Kategori</th>
                <th>Nama</th>
                <th>Spesifikasi</th>
                <th>Kondisi Alat</th>
                <th>Jenis</th>
                <th>Tahun</th>
                <th>Aksi</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(product, i) in products" :key="i">
                <td>{{ i + 1 }}.</td>
                <td>{{ product.tanggal }}</td>
                <td>{{ product.kategori }}</td>
                <td>{{ product.name }}</td>
                <td>{{ product.spesifikasi }}</td>
                <td>{{ product.kondisi }}</td>
                <td>{{ product.jenis }}</td>
                <td>{{ product.tahun }}</td>
                <td>
                  <button @click="editProduct(product)">Edit</button>
                  <button @click="deleteProduct(product.id)">Delete</button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const supabase = useSupabaseClient()
const keyword = ref('')
const products = ref([])
const Totalproducts = ref(0)

const getProducts = async () => {
  const { data, error } = await supabase.from('products').select('*').ilike('name', `%${keyword.value}%`)
  if (error) {
    console.error('Error fetching products:', error)
  } else {
    products.value = data
  }
}

const getTotalProducts = async () => {
  const { count, error } = await supabase.from('products').select('*', { count: 'exact', head: true })
  if (error) {
    console.error('Error fetching total products:', error)
  } else {
    Totalproducts.value = count
  }
}

onMounted(() => {
  getProducts()
  getTotalProducts()
})
</script>
<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.dashboard {
  height: 100%;
}

.content {
  display: flex;
}

.content-main {
  margin-left: 250px;
  /* Memberikan ruang untuk sidebar (menu) */
  padding: 30px;
  width: 100%;
  height: 98vh;
  background-color: #F4F4F4;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
}

.header h1 {
  font-size: 28px;
  font-weight: bold;
  color: #005696;
}

.btn {
  background-color: #FFD700;
  color: black;
  padding: 10px 20px;
  border-radius: 6px;
  font-size: 16px;
  cursor: pointer;
  font-weight: bold;
  transition: background-color 0.3s;
  text-decoration: none;
}

.btn:hover {
  background-color: #FFC107;
}

.table-container {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

table {
  width: 100%;
  border-collapse: collapse;
}

table th,
table td {
  border: 1px solid #ddd;
  padding: 10px;
  text-align: left;
}

table th {
  background-color: #005696;
  color: white;
}

table tr:nth-child(even) {
  background-color: #F4F4F4;
}

.action-btn {
  background-color: #0071BC;
  color: white;
  padding: 5px 10px;
  border-radius: 4px;
  text-decoration: none;
  cursor: pointer;
  margin-left: 10px;
}

.delete-btn {
  background-color: #E53E3E;
}

.logo {
  width: 250px;
  height: auto;
  display: block;
  margin: 20px auto;
}
.form-control {
  font-size: 1.25rem;  /* Ukuran font lebih besar */
  padding: 0.75rem 1rem;  /* Lebih banyak ruang di dalam input */
  border-radius: 0.5rem;  /* Membuat sudut input lebih besar */
  height: 50px;  /* Menambah tinggi input */
}
</style>