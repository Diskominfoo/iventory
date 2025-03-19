<template>
    <div>
        <slot />
    </div>
</template>


<script setup>
const client = useSupabaseClient()
console.log(client)
</script>