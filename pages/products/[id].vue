<template>
  <div class="edit-container">
    <h1>Edit Produk</h1>

    <!-- Form Edit Produk -->
    <form @submit.prevent="updateProducts">
      <label>Nama Produk:</label>
      <input v-model="products.nama" type="text" disabled />

      <label>Kategori:</label>
      <select v-model="products.kategori">
        <option v-for="k in kategori" :key="k.id" :value="k.name">{{ k.name }}</option>
      </select>
      <!-- <input v-model="products.kategori" type="text" required /> -->

      <label>Deskripsi:</label>
      <select v-model="products.description">
        <option v-for="d in deskripsi" :key="d.id" :value="d.name">{{ d.name }}</option>
      </select>
      <!-- <textarea v-model="products.description" required></textarea> -->

      <label>Kondisi Alat:</label>
      <select v-model="products.kondisi">
        <option>Baik</option>
        <option>Rusak</option>
      </select>

      <label>Jumlah:</label>
      <input v-model="products.jumlah" type="number" min="1" required />

      <!-- Tombol Simpan -->
      <button type="submit" class="save-btn">Simpan</button>

      <!-- Tombol Kembali -->
      <router-link to="/products" class="cancel-btn">Batal</router-link>
    </form>
  </div>
</template>

<script setup>
definePageMeta({
  middleware: "auth",
});

const route = useRoute();
const router = useRouter();
const supabase = useSupabaseClient();
const kategori = ref([]);
const deskripsi = ref([]);

async function getKategori() {
  const { data, error } = await supabase.from("kategori").select("*");
  if (error) {
    console.error("Error fetching categories:", error);
  }
  if (data) kategori.value = data;
}

async function getDeskripsi() {
  const { data, error } = await supabase.from("description").select("*");
  if (error) {
    console.error("Error fetching description:", error);
  }
  if (data) deskripsi.value = data;
}

// Default nilai produk agar tidak undefined
const products = ref({
  id: null,
  // nama: "",
  kategori: "",
  description: "",
  kondisi: "Baik",
  jumlah: 1,
});

// Mengambil data produk berdasarkan ID
const getProducts = async () => {
  try {
    const { data, error } = await supabase
      .from("products")
      .select("*")
      .eq("id", route.params.id)
      .single();

    if (error) {
      console.error("Error fetching product:", error);
      alert("Gagal mengambil data produk.");
      return;
    }

    products.value = data;
  } catch (err) {
    console.error("Unexpected error:", err);
  }
};

// Fungsi untuk mengupdate produk
const updateProducts = async () => {
  if (!products.value.id) {
    console.error("ID produk tidak ditemukan.");
    alert("Gagal mengupdate produk: ID tidak valid.");
    return;
  }

  try {
    const { error } = await supabase
      .from("products")
      .update({
        nama: products.value.nama,
        kategori: products.value.kategori,
        description: products.value.description,
        kondisi: products.value.kondisi,
        jumlah: products.value.jumlah,
      })
      .eq("id", products.value.id);

    if (error) {
      console.error("Error updating product:", error);
      alert("Gagal mengupdate produk.");
      return;
    }

    alert("Produk berhasil diperbarui!");
    router.push("/products");
  } catch (err) {
    console.error("Unexpected error:", err);
    alert("Terjadi kesalahan, silakan coba lagi.");
  }
};
onMounted(() => {
  getProducts();
  getKategori();
  getDeskripsi();
});
</script>

<style scoped>
.edit-container {
  max-width: 500px;
  margin: 50px auto;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  background-color: #005696;
  color: white;
}

h1 {
  text-align: center;
  color: #ffd700;
}

form {
  display: flex;
  flex-direction: column;
}

label {
  font-weight: bold;
  margin-top: 10px;
}

input,
textarea,
select {
  padding: 10px;
  margin-top: 5px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.save-btn {
  background-color: #ffd700;
  font-weight: bold;
  color: white;
  padding: 10px;
  margin-top: 20px;
  border: none;
  cursor: pointer;
  font-size: 16px;
  border-radius: 5px;
}

.cancel-btn {
  background-color: red;
  font-weight: bold;
  color: white;
  padding: 10px;
  margin-top: 20px;
  border: none;
  cursor: pointer;
  font-size: 16px;
  border-radius: 5px;
  text-align: center;
}
a {
  text-decoration: none;
}
.save-btn:hover {
  background-color: #ffd788;
}
.cancel-btn:hover {
  background-color: rgb(252, 106, 106);
}
</style>
