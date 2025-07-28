<template>
  <!-- WhosList.vue -->
  <form @submit.prevent="fetchReviews">
    <input v-model="reviewer_name" type="text" placeholder="Enter user name" required />
    <button type="submit">Get List</button>
  </form>
</template>

<script setup>
import { ref } from 'vue'

const reviewer_name = ref('')

const emits = defineEmits(['submit-username'])

const fetchReviews = () => {
  emits('submit-username', reviewer_name.value)
  reviewer_name.value = ''
}
</script>
