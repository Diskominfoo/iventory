<template>
  <div>
    <!-- Header Title -->
    <div class="header-title">Sistem Peminjaman Alat Diskominfo</div>
    <br />
    <!-- Navbar -->
    <div class="navbar">
      <div class="nav-links">
        <router-link to="/peminjamanuser">Peminjaman</router-link>
        <router-link to="/pengembalianuser">Pengembalian</router-link>
        <router-link to="/logout">Logout</router-link>
      </div>
    </div>

    <!-- Main Container -->
    <div class="container">
      <div class="top-bar">
        <div class="title-bar">
          📋 Daftar Pengembalian Alat
          <button class="pinjam-btn">
            <router-link to="/halpeguser">Kembalian Alat</router-link>
          </button>
        </div>
        <div class="data-info">
          Menampilkan {{ pengembalian.length }} dari {{ totalPengembalian }}
        </div>
      </div>

      <!-- Search Bar -->
      <br />
      <form @submit.prevent="getPengembalian">
        <div class="search-wrapper">
          <div class="search-container">
            <span class="search-icon">🔍</span>
            <input v-model="keyword" type="text" placeholder="search" />
          </div>
        </div>
      </form>

      <!-- Table Container -->
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
/* Global Styles */
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  background-color: #f4f4f4;
}

.header-title {
  font-size: 28px;
  font-weight: bold;
  color: #ffd700;
  text-align: center; /* Membuat teks berada di tengah */
  margin: 20px auto; /* Memastikan teks tetap terpusat */
  display: flex;
  justify-content: center;
}

/* Navbar Styles */
.navbar {
  background-color: #005696;
  padding: 12px;
  display: flex;
  justify-content: center;
  border-radius: 12px;
  width: fit-content;
  margin: 15px auto;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.3);
}

.nav-links {
  display: flex;
  gap: 20px;
}

.nav-links a {
  text-decoration: none;
  color: white;
  font-size: 16px;
  padding: 8px 15px;
  border-radius: 8px;
  transition: background 0.3s;
}

.nav-links a:hover {
  background: #0071bc;
}

/* Container Styles */
.container {
  width: 90%;
  margin: auto;
  background-color: white;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.3);
}

/* Top Bar Styles */
.top-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px;
  background-color: #005696;
  border-radius: 8px;
  color: white;
  box-shadow: 0px 3px 10px rgba(0, 0, 0, 0.3);
}

.title-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.data-info {
  margin-top: 1rem;
  margin-bottom: 1rem;
}

/* Search Bar Styles */
.search-wrapper {
  display: flex;
  justify-content: flex-start;
  padding: 10px 20px;
}

.search-container {
  display: flex;
  align-items: center;
  background-color: white;
  color: #6c757d;
  box-shadow: 0px 3px 10px rgba(0, 0, 0, 0.3);
  border-radius: 8px;
  padding: 8px;
  width: 1500px;
}

.search-container input {
  width: 100%;
  border: none;
  outline: none;
  font-size: 14px;
  padding-left: 8px;
}

.search-icon {
  color: gray;
  margin-right: 8px;
}

/* Table Styles */
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

/* Button Styles */
.pinjam-btn {
  background-color: #ffd700;
  margin-left: 10px;
  color: black;
  padding: 8px 15px;
  border-radius: 12px;
  font-size: 14px;
  font-weight: bold;
  cursor: pointer;
  border: none;
  transition: background 0.3s;
}
a {
  text-decoration: none;
  color: black;
}
.pinjam-btn:hover {
  background-color: #ffc107;
}
</style>
