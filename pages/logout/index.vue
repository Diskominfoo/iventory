<template>
    <div>
      sedang keluar...
    </div>
  </template>
  
  
  <script setup>
  const client = useSupabaseClient()
  
  onMounted(() => handleLogout())
  
  async function handleLogout() {
    let { error } = await client.auth.signOut()
    if(!error) navigateTo('/login')
  }
  </script>