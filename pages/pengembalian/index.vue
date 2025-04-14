<template>
  <div class="dashboard">
    <Menu />
    <div class="content">
      <div class="content-main">
        <img src="/img/logo.png" alt="Logo" class="logo" />
        <div class="header">
          <h1>Pengembalian Produk</h1>
          <a href="forpeng" class="btn">Mengembalikan</a>
        </div>

        <div class="my-3">
          <form @submit.prevent="getPengembalian">
            <input
              v-model="keyword"
              type="search"
              class="form-control rounded-5"
              placeholder="Cari nama ..."
            />
          </form>
        </div>

        <div class="my-3 text-muted">
          Menampilkan {{ pengembalian.length }} dari {{ totalPengembalian }}
        </div>

        <div class="table-container">
          <h2>Daftar Pengembalian</h2>
          <br />
          <table>
            <thead>
              <tr>
                <th>NO.</th>
                <th>Tanggal Dikembalikan</th>
                <th>Siapa</th>
                <th>Nama</th>
                <th>Produk</th>
                <th>Keadaan</th>
                <th>Status</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, i) in pengembalian" :key="item.id">
                <td>{{ i + 1 }}.</td>
                <td>{{ item.tanggal_kembali }}</td>
                <td>{{ item.siapa?.nama }}</td>
                <td>{{ item.peminjaman?.nama }}</td>
                <td>{{ item.peminjaman?.alat?.nama }}</td>
                <td>{{ item.kondisi_barang }}</td>
                <td :class="{ 'status-selesai': item.status === 'Selesai' }">
                  {{ item.status || "Menunggu" }}
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
  middleware: "auth",
});

const supabase = useSupabaseClient();
const keyword = ref("");
const pengembalian = ref([]);
const totalPengembalian = ref(0);
const selectedPeminjamanId = ref(null); // ID peminjaman yang akan dikembalikan

// Fungsi untuk mengambil data pengembalian
const getPengembalian = async () => {
  const { data, error } = await supabase
    .from("pengembalian")
    .select(
      `id,
      tanggal_kembali,
      status,
      kondisi_barang,
      siapa:siapa_id (nama),
      peminjaman:peminjaman_id (
        id,
        nama,
        alat:alat_id (nama)
      )`
    )
    .order("tanggal_kembali", { ascending: false })
    .ilike("peminjaman.nama", `%${keyword.value}%`); // Search berdasarkan nama peminjaman

  if (error) {
    console.error("Error fetching pengembalian:", error);
  } else {
    pengembalian.value = data;
  }
};

// Fungsi untuk menghitung total pengembalian
const getTotalPengembalian = async () => {
  const { count, error } = await supabase
    .from("pengembalian")
    .select("id", { count: "exact", head: true });

  if (error) {
    console.error("Error fetching total pengembalian:", error);
  } else {
    totalPengembalian.value = count || 0;
  }
};

// Fungsi untuk memperbarui status peminjaman menjadi "Sudah Dikembalikan"
const updatePeminjamanStatus = async (peminjamanId) => {
  const { error } = await supabase
    .from("peminjaman")
    .update({ status: "Sudah Dikembalikan" })
    .eq("id", peminjamanId);

  if (error) {
    console.error("Error updating peminjaman status:", error);
  } else {
    console.log("Peminjaman status updated successfully.");
  }
};

// Fungsi untuk menyimpan pengembalian
const savePengembalian = async () => {
  const { data, error } = await supabase.from("pengembalian").insert([
    {
      tanggal_kembali: new Date().toISOString(),
      kondisi_barang: "", // Atau sesuai dengan inputan kondisi barang
      peminjaman_id: selectedPeminjamanId.value, // ID peminjaman yang dikembalikan
      status: "Selesai",
    },
  ]);

  if (error) {
    console.error("Error inserting pengembalian:", error);
  } else {
    // Setelah pengembalian berhasil disimpan, perbarui status peminjaman
    await updatePeminjamanStatus(selectedPeminjamanId.value);
  }
};

// Ambil data saat halaman dimuat
onMounted(() => {
  getPengembalian();
  getTotalPengembalian();
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
  background-color: #ffd700;
  color: black;
  padding: 10px 20px;
  border-radius: 6px;
  font-size: 16px;
  cursor: pointer;
  font-weight: bold;
  text-decoration: none;
  border: none;
}

.btn:hover {
  background-color: #ffc107;
}

.table-container {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 5px 5px 5px rgba(0, 0, 0, 0.1);
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 10px;
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
  background-color: #f4f4f4;
}

.status-selesai {
  color: #2d6a4f; /* Hijau tua untuk status selesai */
  font-weight: bold;
}

.logo {
  width: 250px;
  height: auto;
  display: block;
  margin: 0 auto 20px;
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
