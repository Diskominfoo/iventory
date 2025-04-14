<template>
  <div class="container">
    <!-- Header -->
    <div class="header">
      <router-link to="/peminjamanuser">
        <div class="back-arrow"></div>
      </router-link>
      <h2>Formulir Peminjaman</h2>
    </div>

    <!-- Form Peminjaman -->
    <br />
    <div class="form-container">
      <form @submit.prevent="kirimData">
        <!-- PILIH SIAPA -->
        <div class="form-group">
          <label for="siapa">Siapa?</label>
          <select v-model="form.siapa" @change="handleSiapaChange" required>
            <option value="">-- Pilih --</option>
            <option v-for="s in siapaList" :key="s.id" :value="s.id">
              {{ s.nama }}
            </option>
          </select>
        </div>

        <div class="form-group" v-if="form.siapa && !isTamu">
          <label for="peminjam">Namamu?</label>
          <select v-model="form.peminjam_id" @change="syncNama" required>
            <option value="">-- Pilih --</option>
            <option v-for="p in peminjamList" :key="p.id" :value="p.id">
              {{ p.nama }}
            </option>
          </select>
        </div>

        <!-- INPUT MANUAL UNTUK TAMU -->
        <div class="form-group" v-if="isTamu">
          <label for="nama">Nama</label>
          <input
            v-model="form.nama"
            type="text"
            minlength="3"
            placeholder="Tulis nama kamu..."
            required
          />
        </div>

        <!-- PRODUK -->
        <div class="form-group">
          <label for="alat">Pilih produk</label>
          <select v-model="form.alat_id" required>
            <option value="">-- Pilih --</option>
            <option v-for="a in alatList" :key="a.id" :value="a.id">
              {{ a.nama }}
            </option>
          </select>
        </div>

        <!-- JUMLAH -->
        <div class="form-group">
          <label for="jumlah">Jumlah</label>
          <input v-model.number="form.jumlah" type="number" min="1" required />
        </div>

        <!-- KEPERLUAN -->
        <div class="form-group">
          <label for="keperluan">Keperluan</label>
          <textarea v-model="form.keperluan" rows="3" required></textarea>
        </div>

        <p>
          <em>Dengan menekan tombol "Pinjam", saya bertanggung jawab atas barang ini.</em>
        </p>

        <!-- TOMBOL -->
        <div class="form-group">
          <button type="submit" :disabled="loading">
            {{ loading ? "Memproses..." : "Pinjam" }}
          </button>
        </div>
      </form>

      <div class="footer">&copy; Diskominfo Kota Tasikmalaya</div>
    </div>
  </div>
</template>

<script setup>
definePageMeta({
  middleware: "auth",
});

const supabase = useSupabaseClient();
const router = useRouter();
const loading = ref(false);

const siapaList = ref([]);
const peminjamList = ref([]);
const alatList = ref([]);

const form = ref({
  siapa: "",
  peminjam_id: "",
  nama: "",
  alat_id: "",
  jumlah: 1,
  keperluan: "",
  status: "Dipinjam",
});

// Apakah yang dipilih "Tamu"
const isTamu = computed(() => {
  const tamu = siapaList.value.find((s) => s.nama.toLowerCase() === "tamu");
  return form.value.siapa === tamu?.id;
});

// Sinkronisasi nama dari peminjam ke form.nama
const syncNama = () => {
  const selected = peminjamList.value.find((p) => p.id === form.value.peminjam_id);
  form.value.nama = selected?.nama || "";
};

const handleSiapaChange = () => {
  form.value.peminjam_id = "";
  form.value.nama = "";
};

const loadData = async () => {
  try {
    const { data: siapaData } = await supabase.from("siapa").select("*");
    const { data: peminjamData } = await supabase.from("peminjam").select("*");
    const { data: alatData } = await supabase.from("alat").select("*");

    siapaList.value = siapaData || [];
    peminjamList.value = peminjamData || [];
    alatList.value = alatData || [];
  } catch (error) {
    console.error("Gagal memuat data:", error.message);
  }
};

const kirimData = async () => {
  loading.value = true;
  try {
    await supabase.from("peminjaman").insert([
      {
        siapa_id: form.value.siapa,
        peminjam_id: isTamu.value ? null : form.value.peminjam_id,
        nama: form.value.nama,
        alat_id: form.value.alat_id,
        jumlah: form.value.jumlah,
        keperluan: form.value.keperluan,
        status: form.value.status,
      },
    ]);
    router.push("/peminjamanuser");
  } catch (err) {
    console.error("Terjadi kesalahan:", err.message);
  } finally {
    loading.value = false;
  }
};

onMounted(loadData);
</script>

<style scoped>
html,
body {
  height: 100%;
  margin: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

.container {
  background-color: #005696;
  color: white;
  padding: 30px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  width: 800px; /* Sesuaikan lebar di sini */
  height: 700px;
  text-align: center;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.header {
  display: flex;
  align-items: center;
  gap: 10px;
}

.back-arrow {
  width: 0;
  height: 0;
  border-top: 8px solid transparent;
  border-bottom: 8px solid transparent;
  border-right: 12px solid white;
  cursor: pointer;
}

h2 {
  margin: 0;
  color: #ffd700;
  font-size: 22px;
}

.form-group {
  display: flex;
  flex-direction: column;
  margin-bottom: 10px;
  width: 100%;
}

.form-group label {
  font-weight: bold;
  width: 100%;
  margin-bottom: 5px;
  display: flex;
  align-items: center;
}

.form-group select,
.form-group input,
.form-group textarea {
  width: 100%;
  padding: 10px;
  border-radius: 5px;
  border: none;
  font-size: 16px;
  box-sizing: border-box;
}

button {
  background-color: #ffd700;
  color: black;
  padding: 10px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-top: 10px;
  font-weight: bold;
  width: 100%;
}

button:hover {
  background-color: #ffc107;
}

.footer {
  text-align: center;
  margin-top: 10px;
  font-size: 12px;
  color: #f4f4f4;
}
</style>
