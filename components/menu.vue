<template>
  <div class="sidebar">
    <h2>Dashboard Diskominfo</h2>
    <ul>
      <li><router-link to="/dashboard">Home</router-link></li>
      <li><router-link to="/produk">Produk</router-link></li>
      <li><router-link to="/peminjaman">Peminjaman</router-link></li>
      <li><router-link to="/pengembalian">Pengembalian</router-link></li>
      <li><router-link to="/login" class="btn">Logout</router-link></li>
    </ul>
  </div>
</template>

<style scoped>
.sidebar {
  width: 250px;
  background-color: #005696;
  color: white;
  padding: 20px;
  height: 100vh;
  position: fixed;
}

.sidebar h2 {
  font-size: 32px;
  font-weight: bold;
  margin-bottom: 30px;
  color: #FFD700;
  text-align: center;
}

.sidebar ul {
  list-style: none;
  padding-left: 0;
}

.sidebar ul li {
  margin-bottom: 10px;
}

.sidebar ul li a {
  color: white;
  text-decoration: none;
  font-size: 20px;
  display: block;
  padding: 10px;
  border-radius: 4px;
  transition: background-color 0.3s ease;
}

.sidebar ul li a:hover {
  background-color: #0071BC;
}

.sidebar ul li .btn {
  background-color: #FFD700;
  color: black;
  padding: 10px;
  border-radius: 4px;
  font-weight: bold;
  display: block;
  text-align: center;
  transition: background-color 0.3s;
}

.sidebar ul li .btn:hover {
  background-color: #FFC107;
}
</style>
