<template>
  <div class="dashboard">
    <!-- Menggunakan Menu.vue sebagai sidebar -->
    <Menu />

    <!-- Content -->
    <div class="content">
    <div class="content-main">
      <img src="/img/logo.png" alt="Logo" class="logo">
      <div class="header">
        <h1>Daftar Produk</h1>
        <router-link to="/alat" class="btn">Tambah Produk</router-link>
      </div>

      <div class="table-container">
        <table>
          <thead>
            <tr>
              <th>NO.</th>
              <th>Kategori</th>
              <th>Nama</th>
              <th>Spesifikasi</th>
              <th>Kondisi Alat</th>
              <th>Jenis</th>
              <th>Tahun</th>
              <th>Aksi</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>1</td>
              <td>PC</td>
              <td>PC Gigabyte (4)</td>
              <td>Intel i5 12400F / 16GB / GTX 1600 8GB</td>
              <td>Baik</td>
              <td>Aset</td>
              <td>2024</td>
              <td>
                <router-link to="" class="action-btn">Edit</router-link>
                <button class="action-btn delete-btn">Hapus</button>
              </td>
            </tr>
            <tr>
              <td>2</td>
              <td>Laptop</td>
              <td>Laptop MSI (1)</td>
              <td>i5 11400H / 15inch / 8GB / 512GB SSD / RTX 3050 8GB</td>
              <td>minus tapi masih bisa digunakan</td>
              <td>Aset</td>
              <td>2024</td>
              <td>
                <router-link to="" class="action-btn">Edit</router-link>
                <button class="action-btn delete-btn">Hapus</button>
              </td>
            </tr>
          </tbody>
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
  height: 98vh;
  background-color: #F4F4F4;
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
}

table th, table td {
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

.action-btn {
  background-color: #0071BC;
  color: white;
  padding: 5px 10px;
  border-radius: 4px;
  text-decoration: none;
  cursor: pointer;
  margin-left: 10px;
}

.delete-btn {
  background-color: #E53E3E;
}

.logo {
  width: 250px;
  height: auto;
  display: block;
  margin: 20px auto;
}
</style>
