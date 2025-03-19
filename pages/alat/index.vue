<template>
  <div class="dashboard">
    <!-- Menyisipkan Menu Sidebar -->
    <Menu />

    <!-- Content -->
    <div class="content">
      <div class="content-main">
        <div class="header">
          <h1>Tambah Alat</h1>
        </div>

        <div class="form-container">
          <h2>Formulir Tambah Alat</h2>

          <!-- Notifikasi -->
          <div v-if="notif.show" :class="['notification', notif.type]">
            {{ notif.message }}
          </div>

          <form @submit.prevent="kirimData">
            <div class="form-group">
              <label for="name">Nama Alat</label>
              <input v-model="form.name" type="text" id="name" placeholder="Masukkan Nama Alat" required />
            </div>

            <div class="form-group">
              <label for="category">Kategori</label>
              <select v-model="form.kategori" id="category" required>
                <option value="PC">PC</option>
                <option value="Laptop">Laptop</option>
                <option value="Monitor">Monitor</option>
                <option value="Printer">Printer</option>
              </select>
            </div>

            <div class="form-group">
              <label for="specification">Spesifikasi</label>
              <input v-model="form.spesifikasi" type="text" id="specification" placeholder="Masukkan Spesifikasi Alat" required />
            </div>

            <div class="form-group">
              <label for="condition">Kondisi Alat</label>
              <input v-model="form.kondisi" type="text" id="condition" placeholder="Masukkan Kondisi Alat" required />
            </div>

            <div class="form-group">
              <label for="type">Jenis</label>
              <select v-model="form.jenis" id="type" required>
                <option value="Aset">Aset</option>
                <option value="Non Aset">Non Aset</option>
              </select>
            </div>

            <div class="form-group">
              <label for="year">Tahun</label>
              <input v-model="form.tahun" type="number" id="year" placeholder="Masukkan Tahun Alat" required />
            </div>
            
            <div class="form-group">
              <label for="jumlah">Jumlah</label>
              <input v-model="form.jumlah" type="number" id="jumlah" placeholder="Masukkan jumlah Alat" required />
            </div>

            <div class="form-group">
              <button type="submit" :disabled="loading">
                {{ loading ? 'Memproses...' : 'Tambah Alat' }}
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>

const supabase = useSupabaseClient();
const router = useRouter();

// Form data
const form = ref({
  name: "",
  kategori: "",
  spesifikasi: "",
  kondisi: "",
  jenis: "",
  tahun: "",
  jumlah: ""
});

// Status loading
const loading = ref(false);

// Notifikasi
const notif = ref({
  show: false,
  message: '',
  type: 'success'
});

// Fungsi untuk menampilkan notifikasi
const showNotification = (message, type = 'success') => {
  notif.value = {
    show: true,
    message,
    type
  };

  setTimeout(() => {
    notif.value.show = false;
  }, 3000);
};

// Fungsi untuk mengirimkan data ke Supabase
const kirimData = async () => {
  if (loading.value) return;

  try {
    loading.value = true;

    // Format tanggal hari ini
    const tanggalHariIni = new Date().toISOString().split('T')[0];

    // Kirim data ke Supabase
    const { data, error } = await supabase
      .from('products')
      .insert([{
        name: form.value.name,
        kategori: form.value.kategori,
        spesifikasi: form.value.spesifikasi,
        kondisi: form.value.kondisi,
        jenis: form.value.jenis,
        tahun: form.value.tahun,
        jumlah: form.value.jumlah,
        tanggal: tanggalHariIni
      }])
      .select();

    if (error) {
      console.error('Error inserting data:', error);
      showNotification('Terjadi kesalahan saat menambah alat', 'error');
      return;
    }

    console.log('Data berhasil disimpan:', data);

    // Tampilkan notifikasi sukses
    showNotification('Alat berhasil ditambahkan');

    // Reset form setelah sukses
    form.value = {
      name: "",
      kategori: "",
      spesifikasi: "",
      kondisi: "",
      jenis: "",
      tahun: "",
      jumlah: ""
    };

    // Redirect ke halaman products setelah 1.5 detik
    setTimeout(() => {
      router.push('/products');
    }, 1500);

  } catch (err) {
    console.error('Unexpected error:', err);
    showNotification('Terjadi kesalahan yang tidak terduga', 'error');
  } finally {
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
  margin-left: 250px;
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

.form-container {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  position: relative;
}

.form-container h2 {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
  color: #005696;
}

.notification {
  padding: 12px;
  border-radius: 4px;
  margin-bottom: 20px;
  font-weight: bold;
}

.notification.success {
  background-color: #d4edda;
  color: #155724;
  border: 1px solid #c3e6cb;
}

.notification.error {
  background-color: #f8d7da;
  color: #721c24;
  border: 1px solid #f5c6cb;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  font-size: 16px;
  font-weight: bold;
  margin-bottom: 8px;
  display: block;
}

.form-group input, .form-group select {
  width: 100%;
  padding: 12px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 8px;
  outline: none;
}

.form-group input:focus, .form-group select:focus {
  border-color: #0071BC;
}

.form-group button {
  padding: 12px 24px;
  background-color: #FFD700;
  color: black;
  border: none;
  border-radius: 6px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s;
  min-width: 150px;
}

.form-group button:hover {
  background-color: #FFC107;
}

.form-group button:disabled {
  background-color: #ddd;
  cursor: not-allowed;
}
</style>