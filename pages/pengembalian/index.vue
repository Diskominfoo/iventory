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
            <input v-model="keyword" type="search" class="form-control rounded-5" placeholder="Cari nama ..." />
          </form>
        </div>
        
        <div class="my-3 text-muted">Menampilkan {{ pengembalian.length }} dari {{ totalPengembalian }}</div>
        
        <div class="table-container">
          <h2>Daftar Pengembalian</h2>
          <br />
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

const getPengembalian = async () => {
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
    totalPengembalian.value = count || 0;
  }
};

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

.table-container table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 10px;
}

.table-container th,
.table-container td {
  border: 1px solid #ddd;
  padding: 10px;
  text-align: left;
}

.table-container th {
  background-color: #005696;
  color: white;
}

.status-dikembalikan {
  color: green;
  font-weight: bold;
}

.status-terlambat {
  color: #e53e3e;
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
