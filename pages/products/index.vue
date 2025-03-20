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
          <form @submit.prevent="getProducts">
            <input v-model="keyword" type="search" class="form-control rounded-5" placeholder="Cari nama ...">
          </form>
        </div>

        <div class="my-3 text-muted">menampilkan {{ products.length }} dari {{ totalProducts }}</div>

        <div class="table-container">
          <table>
            <thead>
              <tr>
                <th>NO.</th>
                <th>Tanggal</th>
                <th>Nama</th>
                <th>Kategori</th>
                <th>description</th>
                <th>Kondisi Alat</th>
                <th>Jenis</th>
                <th>Tahun</th>
                <th>Jumlah</th>
                <th>Aksi</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(product, i) in products" :key="product.id">
                <td>{{ i + 1 }}.</td>
                <td>{{ product.tanggal }}</td>
                <td>{{ product.name }}</td>
                <td>{{ product.kategori }}</td>
                <td>{{ product.description }}</td>
                <td>{{ product.kondisi }}</td>
                <td>{{ product.jenis }}</td>
                <td>{{ product.tahun }}</td>
                <td>{{ product.jumlah }}</td>
                <td>
                  <button class="action-btn" @click="editProduct(product)">Edit</button>
                  <button class="action-btn delete-btn" @click="deleteProduct(product.id)">Delete</button>
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

definePageMeta({
    middleware:'auth'
})

const supabase = useSupabaseClient();
const router = useRouter();
const keyword = ref('');
const products = ref([]);
const totalProducts = ref(0);

const getProducts = async () => {
  const { data, error } = await supabase.from('products').select('*').ilike('name', `%${keyword.value}%`);
  if (error) {
    console.error('Error fetching products:', error);
  } else {
    products.value = data;
  }
};

const getTotalProducts = async () => {
  const { count, error } = await supabase.from('products').select('*', { count: 'exact', head: true });
  if (error) {
    console.error('Error fetching total products:', error);
  } else {
    totalProducts.value = count;
  }
};

const editProduct = (product) => {
  router.push(`/alat/edit/${product.id}`);
};

const deleteProduct = async (id) => {
  if (confirm('Apakah Anda yakin ingin menghapus produk ini?')) {
    const { error } = await supabase.from('products').delete().eq('id', id);
    if (error) {
      console.error('Error deleting product:', error);
      alert('Gagal menghapus produk');
    } else {
      getProducts();
      getTotalProducts();
    }
  }
};

onMounted(() => {
  getProducts();
  getTotalProducts();
});
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
  padding: 30px;
  width: 100%;
  height: 100vh;
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
  border: none;
  cursor: pointer;
  margin-right: 5px;
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
  font-size: 1.25rem;
  padding: 0.75rem 1rem;
  border-radius: 0.5rem;
  height: 50px;
  width: 100%;
}

.my-3 {
  margin-top: 1rem;
  margin-bottom: 1rem;
}

.text-muted {
  color: #6c757d;
}

.rounded-5 {
  border-radius: 0.5rem;
}
</style>