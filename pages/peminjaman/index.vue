<template>
  <div class="dashboard">
    <Menu />

    <div class="content">
      <div class="content-main">
        <img src="/img/logo.png" alt="Logo" class="logo" />
        <div class="header">
          <h1>Peminjaman Produk</h1>
          <router-link to="/forpin" class="btn">Pinjam</router-link>
        </div>

        <div class="my-3">
          <form @submit.prevent="getpeminjaman">
            <input
              v-model="keyword"
              type="search"
              class="form-control rounded-5"
              placeholder="Cari nama ..."
            />
          </form>
        </div>

        <div class="my-3 text-muted">
          Menampilkan {{ peminjaman.length }} dari {{ totalPeminjaman }}
        </div>

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
  transition: background-color 0.3s;
  text-decoration: none;
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

.status-dipinjam {
  color: #38a169;
  font-weight: bold;
}

.status-dikembalikan {
  color: #e53e3e;
  font-weight: bold;
}

.status-menunggu {
  color: #ffcc00;
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
