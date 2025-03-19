<template>
    <div class="full-container">
      <div class="login-container">
        <div class="logo-container">
          <img src="/img/logo.png" alt="Logo" class="logo" />
        </div>
        <h2>Login</h2>
        <form @submit.prevent="handleLogin">
          <div class="input-group">
            <label for="username">Username:</label>
            <input v-model="email" type="text" id="username" name="username" required />
          </div>
          <div class="input-group">
            <label for="password">Password:</label>
            <input v-model="password" type="password" id="password" name="password" required />
          </div>
          <button type="submit">Login</button>
        </form>
      </div>
    </div>
  </template>
  
  <script setup>
definePageMeta({
  layout: 'login',
})

const client = useSupabaseClient()
const email = ref("")
const password = ref("")
const isPasswordVisible = ref(false)
const errorMessage = ref("")  // Store error message

// Function to handle login
async function handleLogin() {
  const { data, error } = await client.auth.signInWithPassword({
    email: email.value,
    password: password.value,
  })

  if (error) {
    if (error.message.includes("invalid email")) {
      errorMessage.value = "Email yang anda masukkan salah"
    } else if (error.message.includes("incorrect password")) {
      errorMessage.value = "Password yang anda masukkan salah"
    } else {
      errorMessage.value = "Periksa lebih teliti !!!"
    }
    setTimeout(() => {
      errorMessage.value = ""
    }, 1000)
    return
  }

  if (data) {
    getProfileRole(data.user.id)
  }
}

async function getProfileRole(id) {
  const { data, error } = await client
    .from('profile')
    .select('role')
    .eq('id', id)
    .single()
  if (data) {
    if (data.role == 'admin') navigateTo('/dashboard')
    else navigateTo('/peminjamanuser')
  }
}

function togglePasswordVisibility() {
  isPasswordVisible.value = !isPasswordVisible.value
}
</script>
  <style scoped>
  /* Mengatur seluruh halaman agar selalu penuh */
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  
  html, body {
    width: 100vw;
    height: 100vh;
    overflow: hidden;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: #005696;
  }
  
  /* Pastikan background biru penuh */
  .full-container {
    width: 100vw;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: #005696;
  }
  
  /* Kotak login */
  .login-container {
    background-color: #fff;
    padding: 30px;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.5);
    max-width: 400px;
    width: 90%;
    text-align: center;
  }
  
  /* Logo */
  .logo-container {
    margin-bottom: 15px;
  }
  
  .logo {
    width: 200px;
    height: auto;
  }
  
  /* Styling teks */
  h2 {
    margin-bottom: 20px;
    color: #005696;
  }
  
  /* Input form */
  .input-group {
    margin-bottom: 15px;
    text-align: left;
  }
  
  label {
    display: block;
    font-weight: bold;
    margin-bottom: 5px;
  }
  
  input {
    width: 100%;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 16px;
  }
  
  /* Styling tombol */
  button {
    width: 100%;
    padding: 12px;
    background-color: #ffd700;
    color: black;
    border: none;
    border-radius: 6px;
    font-size: 16px;
    cursor: pointer;
    font-weight: bold;
  }
  
  button:hover {
    background-color: #ffc107;
  }
  </style>
  