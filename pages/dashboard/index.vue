<template>
  <div class="dashboard">
    <Menu />
    <!-- Content -->
    <div class="content">
      <img src="/img/logo.png" alt="Logo" class="logo">
      <div class="header">
        <h1>Dashboard Inventory</h1>
      </div>

      <!-- Stats Section -->
      <div class="stats">
        <div class="stat-card">
          <h2>Total Produk</h2>
          <p>120</p>
        </div>
        <div class="stat-card">
          <h2>Dipinjam</h2>
          <p>5</p>
        </div>
        <div class="stat-card">
          <h2>Pengembalian</h2>
          <p>5</p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Reset default styles */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* Layout */
.dashboard {
  display: flex;
  height: 100%;
}

/* Content */
.content {
  margin-left: 250px; /* To prevent content overlap with sidebar */
  padding: 30px;
  width: 100%;
  min-height: 98vh;
  background-color: #F4F4F4; /* Abu Muda */
  overflow: hidden; /* Prevent overflow */
}

.header {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 30px;
}

.header h1 {
  font-size: 32px;
  font-weight: 600;
  color: #005696; /* Biru Tua */
}

/* Button */
.btn {
  background-color: #FFD700; /* Kuning */
  color: black;
  padding: 12px 24px;
  border-radius: 6px;
  font-size: 16px;
  cursor: pointer;
  font-weight: bold;
  transition: background-color 0.3s;
}

.btn:hover {
  background-color: #FFC107; /* Kuning Lebih Gelap */
}

/* Stats Section */
.stats {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 30px;
  margin-bottom: 30px;
}

.stat-card {
  background-color: #005696; /* Biru Tua */
  color: white;
  padding: 40px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.stat-card p {
  font-size: 24px;
}

/* Responsive design for smaller screens */
@media (max-width: 768px) {
  .stats {
    grid-template-columns: 1fr;
  }

  .content {
    margin-left: 0;
    padding: 20px;
  }

  .btn {
    padding: 10px 20px;
  }
}

/* Logo styling */
.logo {
  width: 250px;
  height: auto;
  display: block;
  margin: 20px auto;
}
</style>
