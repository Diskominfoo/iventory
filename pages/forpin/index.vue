<template>
  <div class="dashboard">
    <Menu />
    <div class="content">
      <div class="content-main">
        <div class="form-container">
          <h2>Formulir Peminjaman</h2>
          <form @submit.prevent="kirimData">
            <div class="form-group">
              <label for="siapa">Siapa?</label>
              <select v-model="form.siapa" @change="handleSiapaChange" required>
                <option value="">-- Pilih --</option>
                <option v-for="s in siapaList" :key="s.id" :value="s.id">
                  {{ s.nama }}
                </option>
              </select>
            </div>

            <!-- Pilihan nama dari database jika bukan tamu -->
            <div class="form-group" v-if="form.siapa && !isTamu">
              <label for="peminjam">Namamu?</label>
              <select v-model="form.peminjam_id" required>
                <option value="">-- Pilih --</option>
                <option v-for="p in peminjamList" :key="p.id" :value="p.id">
                  {{ p.nama }}
                </option>
              </select>
            </div>

            <!-- Input manual jika yang dipilih adalah tamu -->
            <div class="form-group" v-if="isTamu">
              <label for="nama_manual">Nama</label>
              <input
                v-model="form.nama_manual"
                type="text"
                minlength="100"
                placeholder="Tulis nama kamu..."
                required
              />
            </div>

            <div class="form-group">
              <label for="alat">Pilih produk</label>
              <select v-model="form.alat_id" required>
                <option value="">-- Pilih --</option>
                <option v-for="a in alatList" :key="a.id" :value="a.id">
                  {{ a.name }}
                </option>
              </select>
            </div>

            <div class="form-group">
              <label for="jumlah">Jumlah</label>
              <input v-model.number="form.jumlah" type="number" min="1" required />
            </div>

            <div class="form-group">
              <label for="keperluan">Keperluan</label>
              <textarea v-model="form.keperluan" rows="3" required></textarea>
            </div>

            <p><em>Dengan menekan tombol "Pinjam", saya bertanggung jawab...</em></p>

            <div class="form-group">
              <button type="submit" :disabled="loading">
                {{ loading ? "Memproses..." : "Pinjam" }}
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
const loading = ref(false);
const siapaList = ref([]);
const peminjamList = ref([]);
const alatList = ref([]);
const form = ref({
  siapa: "", // Menyimpan ID dari tabel "siapa"
  peminjam_id: "",
  nama_manual: "",
  alat_id: "",
  jumlah: 1,
  keperluan: "",
  status: "Dipinjam",
});

// Properti komputasi untuk memeriksa apakah yang dipilih adalah "Tamu"
const isTamu = computed(() => {
  const tamu = siapaList.value.find((s) => s.nama.toLowerCase() === "tamu");
  return form.value.siapa === tamu?.id;
});

// Load data dari Supabase
const loadData = async () => {
  try {
    const { data: siapaData, error: siapaError } = await supabase
      .from("siapa")
      .select("*");
    if (siapaError) throw siapaError;

    const { data: peminjamData, error: peminjamError } = await supabase
      .from("peminjam")
      .select("*");
    if (peminjamError) throw peminjamError;

    const { data: alatData, error: alatError } = await supabase.from("alat").select("*");
    if (alatError) throw alatError;

    siapaList.value = siapaData || [];
    peminjamList.value = peminjamData || [];
    alatList.value = alatData || [];
  } catch (error) {
    console.error("Gagal memuat data:", error.message);
  }
};

// Mengatur ulang peminjam jika memilih "Tamu"
const handleSiapaChange = () => {
  if (isTamu.value) {
    form.value.peminjam_id = "";
  } else {
    form.value.nama_manual = "";
  }
};

const kirimData = async () => {
  loading.value = true;
  try {
    const namaFinal = isTamu.value ? form.value.nama_manual : null;

    await supabase.from("peminjaman").insert([
      {
        siapa_id: form.value.siapa,
        peminjam_id: form.value.peminjam_id || null,
        nama_manual: namaFinal,
        alat_id: form.value.alat_id,
        jumlah: form.value.jumlah,
        keperluan: form.value.keperluan,
        status: "Dipinjam",
      },
    ]);
    router.push("/peminjaman");
  } catch (err) {
    console.error("Terjadi kesalahan:", err.message);
  } finally {
    loading.value = false;
  }
};

onMounted(loadData);
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
  color: #ffd700;
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
  color: #ffd700;
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
  background-color: #ffd700;
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
  background-color: #ffc107;
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
