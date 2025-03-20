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
          <form @submit.prevent="getpengembalian">
            <input v-model="keyword" type="search" class="form-control rounded-5" placeholder="Cari nama ..." />
          </form>
        </div>

        <div class="my-3 text-muted">Menampilkan {{ pengembalian.length }} dari {{ totalpengembalian }}</div>
        <div class="table-container">
          <h2>Daftar Pengembalian</h2>
          <br />
          <table>
            <thead>
              <tr>
                <th>NO.</th>
                <th>Tanggal Pinjam</th>
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
                <td>{{ item.name }}</td>
                <td>{{ item.products_id }}</td>
                <td>{{ item.jumlah }}</td>
                <td>{{ item.keperluan }}</td>
                <td :class="{'status-dipinjam': item.status === 'Dipinjam', 'status-terlambat': item.status === 'Terlambat'}">
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
const router = useRouter();
const keyword = ref("");
const pengembalian = ref([]);
const totalpengembalian = ref(0);

const getpengembalian = async () => {
  const { data, error } = await supabase
    .from("pengembalian")
    .select("*")
    .ilike("name", `%${keyword.value}%`);

  if (error) {
    console.error("Error fetching pengembalian:", error);
  } else {
    pengembalian.value = data;
  }
};

const gettotalPengembalian = async () => {
  const { count, error } = await supabase
    .from("pengembalian")
    .select("*", { count: "exact", head: true });

  if (error) {
    console.error("Error fetching total pengembalian:", error);
  } else {
    totalpengembalian.value = count || 0;
  }
};

onMounted(() => {
  getpengembalian();
  gettotalPengembalian();
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
  background-color: #F4F4F4;
  height: 98vh;
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
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
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
  
  .status-dipinjam {
    color: #e53e3e;
    font-weight: bold;
  }
  
  .status-terlambat {
    color: #e53e3e;
    font-weight: bold;
  }
  
  .status-sudah {
    color: green;
    font-weight: bold;
  }
  
  .confirm-btn {
    background-color: #38a169;
    color: white;
    border: none;
    padding: 8px 12px;
    border-radius: 5px;
    cursor: pointer;
    font-weight: bold;
  }
  
  .confirm-btn:hover {
    background-color: #2f855a;
  }
  
  .logo {
    width: 250px;
    height: auto;
    display: block;
    margin: 0 auto 20px;
  }
  </style>
  