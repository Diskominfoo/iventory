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
          📋 Daftar Peminjaman Alat
          <button class="pinjam-btn">
            <router-link to="/halpijuser">Pinjam Alat</router-link>
          </button>
        </div>
        <div class="data-info">
          Menampilkan {{ peminjaman.length }} dari {{ totalPeminjaman }}
        </div>
      </div>

      <!-- Search Bar -->
      <br />
      <form @submit.prevent="getpeminjaman">
        <div class="search-wrapper">
          <div class="search-container">
            <span class="search-icon">🔍</span>
            <input v-model="keyword" type="text" placeholder="search" />
          </div>
        </div>
      </form>

      <!-- Table Container -->
      <div class="table-container">
        <h2>Daftar Peminjaman</h2>
        <br />
        <table>
          <thead>
            <tr>
              <th>NO.</th>
              <th>Tanggal Pinjam</th>
              <th>Siapa</th>
              <th>Nama</th>
              <th>Produk</th>
              <th>Jumlah</th>
              <th>Keperluan</th>
              <th>Status</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, i) in peminjaman" :key="item.id">
              <td>{{ i + 1 }}.</td>
              <td>{{ item.tanggal_pinjam }}</td>
              <td>{{ item.siapa.nama }}</td>
              <!-- Menampilkan nama siapa dari relasi -->
              <td>{{ item.nama }}</td>
              <td>{{ item.alat.nama }}</td>
              <td>{{ item.jumlah }}</td>
              <td>{{ item.keperluan }}</td>
              <td
                :class="{
                  'status-dipinjam': item.status === 'Dipinjam',
                  'status-dikembalikan': item.status === 'Sudah Dikembalikan',
                  'status-menunggu': !item.status || item.status === 'Menunggu',
                }"
              >
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
const router = useRouter();
const keyword = ref("");
const peminjaman = ref([]);
const totalPeminjaman = ref(0);

// Fungsi untuk mengambil data peminjaman
const getpeminjaman = async () => {
  const { data, error } = await supabase
    .from("peminjaman")
    .select(
      "id, alat_id, alat(nama), nama ,tanggal_pinjam, jumlah, keperluan, status, siapa_id, siapa(nama)"
    ) // Mengambil data dari siapa
    .ilike("nama", `%${keyword.value}%`) // Pencarian nama
    .limit(10); // Menambahkan limit untuk menampilkan sejumlah data

  if (error) {
    console.error("Error fetching peminjaman:", error);
  } else {
    peminjaman.value = data;
  }
};

// Fungsi untuk mengambil jumlah total peminjaman
const gettotalPeminjaman = async () => {
  const { count, error } = await supabase
    .from("peminjaman")
    .select("*", { count: "exact", head: true });

  if (error) {
    console.error("Error fetching total peminjaman:", error);
  } else {
    totalPeminjaman.value = count || 0;
  }
};

// Fungsi dijalankan ketika komponen dimuat
onMounted(() => {
  getpeminjaman();
  gettotalPeminjaman();
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

.status-dipinjam {
  color: #38a169;
  font-weight: bold;
}

.status-terlambat {
  color: #e53e3e;
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
