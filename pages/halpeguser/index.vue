<template>
    <div class="container">
      <div class="header">
        <router-link to="/pengembalianuser">
          <div class="back-arrow"></div>
        </router-link>
        <h2>Pengembalian Alat</h2>
      </div>
  
      <form action="#" method="post">
        <div class="form-group"><br>
          <label for="nama">Nama Peminjam</label>
          <input type="text" id="nama" placeholder="Tulis nama kamu..." required />
        </div>
  
        <div class="form-group">
          <label for="alat">Nama Alat</label>
          <input type="text" id="alat" placeholder="Tulis nama alat yang dikembalikan..." required />
        </div>
  
        <div class="form-group">
          <label for="kondisi">Kondisi Alat</label>
          <textarea id="kondisi" rows="3" placeholder="Deskripsikan kondisi alat..." required></textarea>
        </div>
  
        <p><b>Status akan dikonfirmasi oleh admin.</b></p>
  
        <button type="submit">Kembalikan Alat</button>
      </form>
  
      <div class="footer">&copy; Diskominfo Kota Tasikmalaya</div>
    </div>
  </template>
  
  <style scoped>
  /* Memastikan halaman penuh */
  html, body {
    height: 100%;
    margin: 0;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  /* Container tetap di tengah dengan absolute positioning */
  .container {
    background-color: #0071BC;
    padding: 30px;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    width: 800px;
    height: 700px;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    display: flex;
    flex-direction: column;
    justify-content: center; /* Menjaga konten tetap sejajar di tengah */
    align-items: center;
    text-align: center;
  }
  
  /* Header */
  .header {
    display: flex;
    align-items: center;
    gap: 10px;
  }
  
  .back-arrow {
    width: 0;
    height: 0;
    border-top: 8px solid transparent;
    border-bottom: 8px solid transparent;
    border-right: 12px solid white;
    cursor: pointer;
  }
  
  h2 {
    margin-right: 500px;
    color: #FFD700;
    font-size: 22px;
  }
  
  /* Form */
  form {
    display: flex;
    flex-direction: column;
    gap: 15px;
    width: 100%;
    max-width: 700px; /* Membatasi lebar form agar tidak terlalu lebar */
  }
  
  /* Input dan textarea */
  .form-group {
    display: flex;
    flex-direction: column;
    text-align: left;
    width: 100%;
  }
  
  .form-group label {
    font-weight: bold;
    margin-bottom: 5px;
  }
  
  .form-group input,
  .form-group textarea {
    width: 100%;
    padding: 12px;
    border-radius: 5px;
    border: none;
    font-size: 16px;
    box-sizing: border-box; 
  }
  
  /* Tombol */
  button {
    background-color: #FFD700;
    color: black;
    padding: 12px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-weight: bold;
    width: 100%;
  }
  
  button:hover {
    background-color: #FFC107;
  }
  
  /* Footer */
  .footer {
    text-align: center;
    margin-top: 15px;
    font-size: 12px;
    color: #F4F4F4;
  }
  </style>
  