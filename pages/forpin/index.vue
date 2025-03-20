<template>
  <div class="dashboard">
      <!-- Menyisipkan Menu Sidebar -->
      <Menu /> 

      <div class="content">
          <div class="content-main">

              <!-- Form Peminjaman -->
              <div class="form-container">
              <h2>Formulir Peminjaman</h2>
                  <form @submit.prevent="kirimData">
                      <div class="form-group">
                          <label for="siapa">Siapa?</label>
                          <select v-model="form.siapa" required>
                              <option value="">-- Pilih --</option>
                              <option value="Staff">Staff</option>
                              <option value="Magang">Magang</option>
                              <option value="Client">Tamu</option>
                          </select>
                      </div>

                      <div class="form-group">
                          <label for="namamu">Namamu?</label>
                          <input v-model="form.name" type="text" placeholder="Tulis nama kamu..." required />
                      </div>

                      <div class="form-group">
                          <label for="alat">Pilih produk</label>
                          <select v-model="form.products_id" required>
                              <option value="">-- Pilih --</option>
                              <option value="Laptop">Laptop</option>
                              <option value="Proyektor">Proyektor</option>
                              <option value="Kamera">Kamera</option>
                          </select>
                      </div>

                      <div class="form-group">
                          <label for="jumlah">Jumlah</label>
                          <input v-model.number="form.jumlah" type="number" min="1" placeholder="Jumlah Barang Yang Akan Dipinjam" required />
                      </div>

                      <div class="form-group">
                          <label for="keperluan">Keperluannya apa?</label>
                          <textarea v-model="form.keperluan" rows="3" placeholder="Tulis keperluan meminjam alat ini..." required></textarea>
                      </div>

                      <p><em>Dengan menekan tombol "Pinjam", saya bertanggung jawab terhadap alat/barang yang dipinjam sesuai dengan <u>SOP poin 2.i.</u></em></p>

                      <div class="form-group">
                          <button type="submit" :disabled="loading">
                              {{ loading ? 'Memproses...' : 'Tambah Alat' }}
                          </button>
                      </div>
                  <div class="footer">&copy; Diskominfo Kota Tasikmalaya</div>
                  </form>
              </div>
          </div>
      </div>
  </div>
</template>

<script setup>

definePageMeta({
  middleware: 'auth'
});

const supabase = useSupabaseClient();
const router = useRouter();
const loading = ref(false);

// Form data
const form = ref({
  siapa: "",
  name: "",
  products_id: "",
  jumlah: 1,
  keperluan: ""
});

// Fungsi untuk mengirimkan data ke Supabase
const kirimData = async () => {
  loading.value = true;
  try {
      const { data, error } = await supabase
          .from("products")
          .insert([form.value])
          .select();
      
      if (error) {
          throw error;
      }

      loading.value = false;
      router.push('/peminjaman');
  } catch (err) {
      console.error("Terjadi kesalahan:", err.message);
      loading.value = false;
  }
};
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
  margin-left: 250px; /* Memberikan ruang untuk sidebar (menu) */
  padding: 30px;
  width: 100%;
  height: auto;
}

.header {
    display: flex;
    align-items: center;
    gap: 10px;
  }
  
  h2 {
    margin: 0;
    color: #FFD700;
    font-size: 24px;
  }

/* Form Styling */
.form-container {
  background-color: #005696;
  color: white;
  padding: 30px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.form-container h2 {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
  color: #FFD700;
}

.form-group {
  display: flex;
  flex-direction: column;
  margin-bottom: 15px;
  width: 100%;
}

.form-group label {
  font-weight: bold;
  margin-bottom: 5px;
}

.form-group select,
.form-group input,
.form-group textarea {
  width: 100%;
  padding: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
  font-size: 16px;
  box-sizing: border-box;
}

button {
  background-color: #FFD700;
  color: black;
  padding: 10px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-top: 10px;
  font-weight: bold;
  width: 100%;
  transition: background 0.3s ease;
}

button:hover {
  background-color: #FFC107;
}

button:disabled {
  background-color: #888;
  cursor: not-allowed;
}

.footer {
  text-align: center;
  margin-top: 20px;
  font-size: 12px;
}
</style>
