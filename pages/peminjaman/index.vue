<template>
    <div class="dashboard">
         <Menu />

  <div class="content">
    <div class="content-main">
      <img src="/img/logo.png" alt="Logo" class="logo">
      <div class="header">
        <h1>Peminjaman Produk</h1>
        <router-link to="/formpeminjaman" class="btn">Pinjam</router-link>
      </div>

      <div class="table-container">
        <h2>Daftar Peminjaman</h2>
        <table>
          <thead>
            <tr>
              <th>No</th>
              <th>Nama</th>
              <th>Barang</th>
              <th>Tanggal Pinjam</th>
              <th>Jumlah Yang Dipinjam</th>
              <th>Status</th>
            </tr>
          </thead>
        </table>
      </div>
    </div>
  </div>
</div>
</template>

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
  background-color: #FFD700;
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
  background-color: #FFC107;
}

.table-container {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
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
  background-color: #F4F4F4;
}

.status-dipinjam {
  color: #38a169;
  font-weight: bold;
}

.status-terlambat {
  color: #E53E3E;
  font-weight: bold;
}

.logo {
  width: 250px;
  height: auto;
  display: block;
  margin: 0 auto 20px;
}
</style>
