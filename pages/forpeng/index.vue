<template>
  <div class="dashboard">
    <Menu />

    <div class="content">
      <div class="content-main">
        <div class="form-container">
          <h2>Formulir Pengembalian</h2>
          <form @submit.prevent="kirimData">
            <!-- Pilih Siapa -->
            <div class="form-group">
              <label for="siapa_id">Pilih Siapa</label>
              <select v-model="form.siapa_id" required>
                <option value="">-- Pilih Siapa --</option>
                <option v-for="siapa in siapaList" :key="siapa.id" :value="siapa.id">
                  {{ siapa.nama }}
                </option>
              </select>
            </div>

            <!-- Pilih Nama dari Peminjaman -->
            <div class="form-group" v-if="form.siapa_id">
              <label for="peminjaman_id">Nama</label>
              <select v-model="form.peminjaman_id" required>
                <option value="">-- Pilih Nama --</option>
                <option
                  v-for="p in peminjamanList.filter((p) => p.siapa_id === form.siapa_id)"
                  :key="p.id"
                  :value="p.id"
                >
                  {{ p.nama }}
                </option>
              </select>
            </div>

            <!-- Pilih Alat -->
            <div class="form-group" v-if="form.peminjaman_id">
              <label for="alat_id">Pilih Alat</label>
              <select v-model="form.alat_id" required>
                <option value="">-- Pilih Alat --</option>
                <option v-for="alat in filteredAlat" :key="alat.id" :value="alat.id">
                  {{ alat.nama }}
                </option>
              </select>
            </div>

            <!-- Input Keadaan -->
            <div class="form-group">
              <label for="kondisi_barang">Keadaan Barang</label>
              <textarea
                v-model="form.kondisi_barang"
                rows="2"
                placeholder="Tulis keadaan setelah meminjam alat ini..."
                required
              ></textarea>
            </div>

            <p>
              <em>
                Dengan menekan tombol "Mengembalikan", saya sudah bertanggung jawab
                terhadap alat/barang yang telah dipinjam sesuai dengan
                <u>SOP poin 2.i.</u>
              </em>
            </p>

            <!-- Tombol Submit -->
            <div class="form-group">
              <button type="submit" :disabled="loading">
                {{ loading ? "Memproses..." : "Kembalikan Alat" }}
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
definePageMeta({ middleware: "auth" });

const supabase = useSupabaseClient();
const router = useRouter();
const loading = ref(false);

const form = ref({
  peminjaman_id: "",
  siapa_id: "",
  alat_id: "",
  kondisi_barang: "",
});

const peminjamanList = ref([]);
const siapaList = ref([]);
const alatList = ref([]);

const getPeminjamanList = async () => {
  const { data, error } = await supabase.from("peminjaman").select("*");
  if (error) console.error("Error fetching peminjaman list:", error);
  else peminjamanList.value = data;
};

const getSiapaList = async () => {
  const { data, error } = await supabase.from("siapa").select("*");
  if (error) console.error("Error fetching siapa list:", error);
  else siapaList.value = data;
};

const getAlatList = async () => {
  const { data, error } = await supabase.from("alat").select("*");
  if (error) console.error("Error fetching alat list:", error);
  else alatList.value = data;
};

const updatePeminjamanStatus = async (peminjamanId) => {
  const { error } = await supabase
    .from("peminjaman")
    .update({ status: "Dikembalikan" })
    .eq("id", peminjamanId);

  if (error) {
    console.error("Error updating peminjaman status:", error);
  } else {
    console.log("Status peminjaman diperbarui menjadi 'Dikembalikan'.");
  }
};

const kirimData = async () => {
  if (
    !form.value.siapa_id ||
    !form.value.peminjaman_id ||
    !form.value.alat_id ||
    !form.value.kondisi_barang
  ) {
    alert("Harap lengkapi semua kolom!");
    return;
  }

  loading.value = true;
  try {
    const { error } = await supabase.from("pengembalian").insert([
      {
        peminjaman_id: form.value.peminjaman_id,
        siapa_id: form.value.siapa_id,
        alat_id: form.value.alat_id,
        kondisi_barang: form.value.kondisi_barang,
        status: "Selesai",
        tanggal_kembali: new Date().toISOString(),
      },
    ]);

    if (error) throw error;

    // Perbarui status peminjaman menjadi 'Dikembalikan'
    await updatePeminjamanStatus(form.value.peminjaman_id);

    router.push("/pengembalian");
  } catch (err) {
    console.error("Terjadi kesalahan:", err.message);
  } finally {
    loading.value = false;
  }
};

onMounted(() => {
  getPeminjamanList();
  getSiapaList();
  getAlatList();
});

const filteredAlat = computed(() => {
  if (form.value.peminjaman_id) {
    const peminjaman = peminjamanList.value.find(
      (p) => p.id === form.value.peminjaman_id
    );
    if (peminjaman) {
      return alatList.value.filter((alat) => alat.id === peminjaman.alat_id);
    }
  }
  return [];
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
