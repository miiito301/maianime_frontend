<template>
  <div>
    <h2>ユーザーのアニメリスト</h2>
    <div v-if="animeList.length === 0">アニメが見つかりません。</div>
    <div v-else class="anime-grid">
      <div v-for="anime in animeList" :key="anime.id" class="anime-card">
        <img :src="anime.imageUrl || '/no-image.jpg'" :alt="anime.title" class="poster" />
        <p>{{ anime.title }}</p>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  animeList: {
    type: Array,
    required: true
  }
})
</script>


