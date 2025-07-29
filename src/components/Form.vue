<template>
<!--Form.vue-->
    <h1 class="form-title">MAIANIME</h1>
    <form @submit.prevent="submitForm">
        <input type="text" v-model="title" placeholder="Enter Anime Title" />
        <button type="submit">Search</button>
    </form>
    
</template>

<script setup>

import { ref } from 'vue'
const title = ref('')

const submitForm=()=>{
    emits("submit-form",title.value)
    title.value=""
}

const emits=defineEmits([
    "submit-form"
])

</script>