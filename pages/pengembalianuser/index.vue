<template>
  <div>
    <!-- Header Title -->
    <div class="header-title">Sistem Peminjaman Alat Diskominfo</div><br>
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
        <div class="data-info">Menampilkan {{ pengembalian.length }} dari {{ TotalPengembalian }}</div>
      </div>

      <!-- Search Bar --> 
      <form @submit.prevent="getpengembalian">
      <div class="search-wrapper">
        <div class="search-container">
          <span class="search-icon">🔍</span>
          <input v-model="keyword" type="text" placeholder="search">
        </div>
      </div>
      </form>

      <!-- Table Container -->
      <div class="table-container">
        <table>
            <thead>
              <tr>
                <th>NO.</th>
                <th>Tanggal Dikembalikan</th>
                <th>siapa</th>
                <th>Nama</th>
                <th>Produk</th>
                <th>Keadaan</th>
                <th>Status</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, i) in pengembalian" :key="item.id">
                <td>{{ i + 1 }}.</td>
                <td>{{ item.tanggal_pengembalian }}</td>
                <td>{{ item.siapa }}</td>
                <td>{{ item.peminjaman_id }}</td>
                <td>{{ item.product_id }}</td>
                <td>{{ item.keadaan }}</td>
                <td :class="{'status-dikembalikan': item.status === 'Dikembalikan', 'status-terlambat': item.status === 'Terlambat'}">
                  {{ item.status || 'Menunggu' }}
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
const pengembalian = ref([]);
const TotalPengembalian = ref(0);

const getpengembalian = async () => {
  const { data, error } = await supabase
    .from("pengembalian")
    .select("*")
    .ilike("peminjaman_id", `%${keyword.value}%`);

  if (error) {
    console.error("Error fetching pengembalian:", error);
  } else {
    pengembalian.value = data;
  }
};

const getTotalPengembalian = async () => {
  const { count, error } = await supabase
    .from("pengembalian")
    .select("id", { count: "exact", head: true });

  if (error) {
    console.error("Error fetching total pengembalian:", error);
  } else {
    TotalPengembalian.value = count || 0;
  }
};

onMounted(() => {
  getpengembalian();
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
margin: 20px auto;  /* Memastikan teks tetap terpusat */
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
  width: 100%;
  overflow-x: auto;
  margin-top: 10px;
}

table {
  width: 100%;
  border-collapse: collapse;
  background-color: white;
  border-radius: 8px;
  box-shadow: 0px 3px 10px rgba(0, 0, 0, 0.3);
}

th, td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

th {
  background-color: #005696;
  color: white;
}

/* Status Styles */
.status {
  padding: 6px;
  border-radius: 6px;
  font-weight: bold;
  text-align: center;
}

.status-dipinjam {
color: #38a169;
font-weight: bold;
}

.status-kembali {
  background-color: #73a839;
  color: white;
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
a{
  text-decoration: none;
  color: black;
}
.pinjam-btn:hover {
  background-color: #ffc107;
}
</style>
