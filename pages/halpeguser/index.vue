<template>
    <div class="container">
      <div class="header">
        <router-link to="/pengembalianuser">
          <div class="back-arrow"></div>
        </router-link>
        <h2>Formulir Pengembalian</h2>
      </div>
  
      <form @submit.prevent="kirimData">

<div class="form-group"><br>
    <label for="namamu">Nama</label>
    <input v-model="form.peminjaman_id" type="text" placeholder="Tulis nama kamu..." required />
</div>

<div class="form-group">
    <label for="alat">siapa</label>
    <select v-model="form.siapa" required>
        <option value="">-- Pilih --</option>
        <option value="Laptop">staff</option>
        <option value="Proyektor">magang</option>
        <option value="Kamera">tamu</option>
    </select>
</div>
<div class="form-group">
    <label for="alat">Product</label>
    <select v-model="form.product_id" required>
        <option value="">-- Pilih --</option>
        <option value="Laptop">Laptop</option>
        <option value="Proyektor">Proyektor</option>
        <option value="Kamera">Kamera</option>
    </select>
</div>

<div class="form-group">
    <label for="keadaan">Keadaan</label>
    <textarea v-model="form.keadaan" rows="3" placeholder="Tulis keadaan setalh meminjam alat ini..." required></textarea>
</div>

<p><em>Dengan menekan tombol "Kembalikan alat", saya sudah bertanggung jawab terhadap alat/barang yang telah dipinjam sesuai dengan <u>SOP poin 2.i.</u></em></p>

<div class="form-group">
    <button type="submit" :disabled="loading">
        {{ loading ? 'Memproses...' : 'kembalikan alat' }}
    </button>
</div>
<div class="footer">&copy; Diskominfo Kota Tasikmalaya</div>
</form>
    </div>
  </template>
  
  <script setup>

definePageMeta({
  middleware: 'auth'
});

const supabase = useSupabaseClient();
const router = useRouter();
const loading = ref(false);

// Form data
const form = ref({
  siapa: "",
  peminjaman_id: "",
  product_id: "",
  keadaan: ""
});

// Fungsi untuk mengirimkan data ke Supabase
const kirimData = async () => {
  loading.value = true;
  try {
      const { data, error } = await supabase
          .from("pengembalian")
          .insert([form.value])
          .select();
      
      if (error) {
          throw error;
      }

      loading.value = false;
      router.push('/pengembalianuser');
  } catch (err) {
      console.error("Terjadi kesalahan:", err.message);
      loading.value = false;
  }
};
</script>

  
  <style scoped>
  html, body {
  height: 100%;
  margin: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

.container {
  background-color: #005696;
  color: white;
  padding: 30px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  width: 800px; /* Sesuaikan lebar di sini */
  height: 700px;
  text-align: center;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

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
    margin: 0;
    color: #FFD700;
    font-size: 22px;
  }
  
  .form-group {
    display: flex;
    flex-direction: column;
    margin-bottom: 10px;
    width: 100%;
  }
  
  .form-group label {
    font-weight: bold;
    width: 100%;
    margin-bottom: 5px;
    display: flex;
    align-items: center;
  }
  
  .form-group select,
  .form-group input,
  .form-group textarea {
    width: 100%;
    padding: 10px;
    border-radius: 5px;
    border: none;
    font-size: 16px;
    box-sizing: border-box;
  }
  
  button {
    background-color: #FFD700;
    color: black;
    padding: 10px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    margin-top: 10px;
    font-weight: bold;
    width: 100%;
  }
  
  button:hover {
    background-color: #FFC107;
  }
  
  .footer {
    text-align: center;
    margin-top: 10px;
    font-size: 12px;
    color: #F4F4F4;
  }
  </style>
  