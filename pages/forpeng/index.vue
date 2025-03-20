<template>
    <div class="min-h-screen">
      <!-- Sidebar -->
      <Menu />
  
      <!-- Content -->
      <div class="content">
    <div class="content-main">
        <img src="/img/logo.png" alt="Logo" class="logo">
        <div class="header">
          <h1>Pengembalian Produk</h1>
        </div>
  
        <!-- Form Pengembalian -->
        <div class="form-container">
          <h2>Formulir Pengembalian</h2>
          <form>
            <div class="mb-4">
              <label for="nama-peminjam">Nama Pengembali</label>
              <input type="text" id="nama-peminjam" placeholder="Masukkan nama">
            </div>
            <div class="mb-4">
              <label for="barang">Barang yang Dikembalikan</label>
              <input type="text" id="barang" placeholder="Masukkan nama barang">
            </div>
            <div class="mb-4">
              <label for="tanggal-pinjam">Keadaan</label>
              <input type="text" id="tanggal-pinjam" placeholder="kondisi barang yang dipinjam">
            </div>
            <button type="submit">Kirim</button>
          </form>
        </div>
      </div>
    </div>
    </div>
  </template>
  
  <script setup>
  definePageMeta({
    middleware:'auth'
})
  </script>

  <style scoped>
  /* Reset styling */
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
    padding: 12px 24px;
    border-radius: 6px;
    font-size: 16px;
    text-decoration: none;
    font-weight: bold;
    transition: background-color 0.3s;
  }
  
  .btn:hover {
    background-color: #FFC107;
  }
  
  /* Form Styling */
  .form-container {
    background-color: white;
    padding: 30px;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }
  
  .form-container h2 {
    font-size: 24px;
    font-weight: bold;
    margin-bottom: 20px;
    color: #005696;
  }
  
  .form-container form {
    margin-bottom: 15px;
    text-align: left;
  }
  
  .form-container form .mb-4 {
    margin-bottom: 15px;
  }
  
  .form-container form label {
    display: block;
    font-weight: bold;
    margin-bottom: 5px;
  }
  
  .form-container form input {
    width: 100%;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 16px;
  }
  
  .form-container button {
    padding: 12px 24px;
    background-color: #FFD700;
    color: black;
    border: none;
    border-radius: 6px;
    font-size: 16px;
    cursor: pointer;
    font-weight: bold;
  }
  
  .form-container button:hover {
    background-color: #FFC107;
  }
  
  .logo {
  width: 250px;
  height: auto;
  display: block;
  margin: 20px auto;
}
  </style>
  