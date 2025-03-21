<template>
  <div class="dashboard">
    <Menu />
    <div class="content">
      <div class="content-main">
        <div class="form-container">
          <h2>Formulir Tambah Alat</h2>

          <div v-if="notif.show" :class="['notification', notif.type]">
            {{ notif.message }}
          </div>

          <form @submit.prevent="kirimData">
            <!-- Nama Alat (Dropdown dari Supabase) -->
            <div class="form-group">
              <label for="name">Nama Alat</label>
              <select v-model="form.name" id="name" required>
                <option value="">-- Pilih --</option>
                <option v-for="alat in alatList" :key="alat.id" :value="alat.name">
                  {{ alat.name }}
                </option>
              </select>
            </div>

            <!-- Kategori (Dropdown dari Supabase) -->
            <div class="form-group">
              <label for="category">Kategori</label>
              <select v-model="form.kategori" id="category" required>
                <option value="">-- Pilih --</option>
                <option v-for="kat in kategoriList" :key="kat.id" :value="kat.name">
                  {{ kat.name }}
                </option>
              </select>
            </div>

            <!-- Deskripsi (Dropdown dari Supabase) -->
            <div class="form-group">
              <label for="description">Deskripsi</label>
              <select v-model="form.description" id="description" required>
                <option value="">-- Pilih --</option>
                <option v-for="desc in descriptionList" :key="desc.id" :value="desc.name">
                  {{ desc.name }}
                </option>
              </select>
            </div>

            <!-- Kondisi Alat -->
            <div class="form-group">
              <label for="condition">Kondisi Alat</label>
              <select v-model="form.kondisi" id="condition" required>
                <option value="">-- Pilih --</option>
                <option value="Baik">Baik</option>
                <option value="Rusak">Rusak</option>
              </select>
            </div>

            <!-- Jenis (Dropdown dari Supabase) -->
            <div class="form-group">
              <label for="type">Jenis</label>
              <select v-model="form.jenis" id="type" required>
                <option value="">-- Pilih --</option>
                <option v-for="j in jenisList" :key="j.id" :value="j.name">
                  {{ j.name }}
                </option>
              </select>
            </div>

            <!-- Tahun -->
            <div class="form-group">
              <label for="year">Tahun</label>
              <input
                v-model="form.tahun"
                type="number"
                id="year"
                placeholder="Masukkan Tahun Alat"
                required
              />
            </div>

            <!-- Jumlah -->
            <div class="form-group">
              <label for="jumlah">Jumlah</label>
              <input
                v-model="form.jumlah"
                type="number"
                id="jumlah"
                placeholder="Masukkan jumlah Alat"
                required
              />
            </div>

            <!-- Tombol Submit -->
            <div class="form-group">
              <button type="submit" :disabled="loading">
                {{ loading ? "Memproses..." : "Tambah Alat" }}
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
definePageMeta({
  middleware: "auth",
});

const supabase = useSupabaseClient();
const router = useRouter();

// Form data
const form = ref({
  name: "",
  kategori: "",
  description: "",
  kondisi: "",
  jenis: "",
  tahun: "",
  jumlah: "",
});

// Status loading
const loading = ref(false);

// Notifikasi
const notif = ref({ show: false, message: "", type: "success" });

// Fungsi untuk menampilkan notifikasi
const showNotification = (message, type = "success") => {
  notif.value = { show: true, message, type };
  setTimeout(() => {
    notif.value.show = false;
  }, 3000);
};

// Fetch Data untuk Dropdown
const { data: alatList } = await useAsyncData("alat", async () => {
  const { data } = await supabase.from("alat").select("*");
  return data || [];
});

const { data: kategoriList } = await useAsyncData("kategori", async () => {
  const { data } = await supabase.from("kategori").select("*");
  return data || [];
});

const { data: descriptionList } = await useAsyncData("description", async () => {
  const { data } = await supabase.from("description").select("*");
  return data || [];
});

const { data: jenisList } = await useAsyncData("jenis", async () => {
  const { data } = await supabase.from("jenis").select("*");
  return data || [];
});

// Fungsi untuk mengirimkan data ke Supabase
const kirimData = async () => {
  try {
    loading.value = true;

    const tanggalHariIni = new Date().toISOString().split("T")[0];

    const { error } = await supabase.from("products").insert([
      {
        name: form.value.name,
        kategori: form.value.kategori,
        description: form.value.description,
        kondisi: form.value.kondisi,
        jenis: form.value.jenis,
        tahun: form.value.tahun,
        jumlah: form.value.jumlah,
        tanggal: tanggalHariIni,
      },
    ]);

    if (error) {
      console.error("Error inserting data:", error);
      showNotification("Terjadi kesalahan saat menambah alat", "error");
      return;
    }

    showNotification("Alat berhasil ditambahkan");

    setTimeout(() => {
      router.push("/products");
    }, 1500);
  } catch (err) {
    console.error("Unexpected error:", err);
    showNotification("Terjadi kesalahan yang tidak terduga", "error");
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
  height: 100vh;
}

.form-container {
  background-color: #005696;
  color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  position: relative;
}

.form-container h2 {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
  color: #ffd700;
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

.form-group input,
.form-group select {
  width: 100%;
  padding: 12px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 8px;
  outline: none;
}

.form-group input:focus,
.form-group select:focus {
  border-color: #0071bc;
}

.form-group button {
  padding: 12px 24px;
  background-color: #ffd700;
  color: black;
  border: none;
  border-radius: 6px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s;
  min-width: 150px;
}

.form-group button:hover {
  background-color: #ffc107;
}

.form-group button:disabled {
  background-color: #ddd;
  cursor: not-allowed;
}
</style>
