<template>
  <div class="dashboard">
    <!-- Menyisipkan Menu Sidebar -->
    <Menu /> 

    <div class="content">
    <div class="content-main">
      <div class="header">
        <h1>Tambah Alat</h1>
      </div>

      <div class="form-container">
        <h2>Formulir Tambah Alat</h2>

          <div class="form-group">
            <label for="name">Nama Alat</label>
            <input type="text" id="name" placeholder="Masukkan Nama Alat" required>
          </div>

          <div class="form-group">
            <label for="category">Kategori</label>
            <select id="category" required>
              <option value="PC">PC</option>
              <option value="Laptop">Laptop</option>
              <option value="Monitor">Monitor</option>
              <option value="Printer">Printer</option>
            </select>
          </div>

          <div class="form-group">
            <label for="specification">Spesifikasi</label>
            <input type="text" id="specification"  placeholder="Masukkan Spesifikasi Alat" required>
          </div>

          <div class="form-group">
            <label for="specification">Kondisi Alat</label>
            <input type="text" id="specification"  placeholder="Masukkan Spesifikasi Alat" required>
          </div>

          <div class="form-group">
            <label for="type">Jenis</label>
            <select id="type" required>
              <option value="Aset">Aset</option>
              <option value="Non Aset">Non Aset</option>
            </select>
          </div>

          <div class="form-group">
            <label for="year">Tahun</label>
            <input type="year" id="year" placeholder="Masukkan Tahun Alat" required>
          </div>

          <div class="form-group">
            <button type="submit">Tambah Alat</button>
          </div>
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

.form-container {
  background-color: white;
  padding: 10px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.form-container h2 {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
  color: #005696;
}

.form-group {
  margin-bottom: 20px;
  margin-right: 20px;
}

.form-group label {
  font-size: 16px;
  font-weight: bold;
  margin-bottom: 8px;
  display: block;
}

.form-group input, .form-group select {
  width: 100%;
  padding: 12px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 8px;
  outline: none;
}

.form-group input:focus, .form-group select:focus {
  border-color: #0071BC;
}

.form-group button {
  padding: 12px 24px;
  background-color: #FFD700;
  color: black;
  border: none;
  border-radius: 6px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.form-group button:hover {
  background-color: #FFC107;
}


</style>
