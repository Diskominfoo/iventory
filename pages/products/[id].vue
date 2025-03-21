<template>
  <div class="edit-container">
    <h1>Edit Produk</h1>

    <!-- Form Edit Produk -->
    <form @submit.prevent="updateProduct">
      <label>Nama Produk:</label>
      <input v-model="product.name" type="text" required />

      <label>Kategori:</label>
      <input v-model="product.kategori" type="text" required />

      <label>Deskripsi:</label>
      <textarea v-model="product.description" required></textarea>

      <label>Kondisi Alat:</label>
      <select v-model="product.kondisi">
        <option>Baik</option>
        <option>Rusak</option>
      </select>

      <label>Jumlah:</label>
      <input v-model="product.jumlah" type="number" required />

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

// Default nilai produk agar tidak undefined
const product = ref({
  id: null,
  name: "",
  kategori: "",
  description: "",
  kondisi: "Baik",
  jumlah: 1,
});

// Mengambil data produk berdasarkan ID
const getProduct = async () => {
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

    product.value = data;
  } catch (err) {
    console.error("Unexpected error:", err);
  }
};

// Fungsi untuk mengupdate produk
const updateProduct = async () => {
  if (!product.value.id) {
    console.error("ID produk tidak ditemukan.");
    alert("Gagal mengupdate produk: ID tidak valid.");
    return;
  }

  try {
    const { error } = await supabase
      .from("products")
      .update({
        name: product.value.name,
        kategori: product.value.kategori,
        description: product.value.description,
        kondisi: product.value.kondisi,
        jumlah: product.value.jumlah,
      })
      .eq("id", product.value.id);

    if (error) {
      console.error("Error updating product:", error);
      alert("Gagal mengupdate produk.");
      return;
    }

    alert("Produk berhasil diperbarui!");
    router.push("/products"); // Redirect ke halaman daftar produk
  } catch (err) {
    console.error("Unexpected error:", err);
    alert("Terjadi kesalahan, silakan coba lagi.");
  }
};

// Memuat data saat halaman dibuka
onMounted(() => {
  getProduct();
});
</script>

<style scoped>
/* Styling Halaman Edit */
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
