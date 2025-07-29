<template>
  <div class="anime-shelves">
    <!-- ✅ ローディング中の表示 -->
    <div v-if="isLoading" class="loading">
      <div class="spinner"></div>
      <p>アニメ一覧を取得中...</p>
    </div>

    <!-- ✅ アニメ表示 -->
    <div v-else>
      <div
        v-for="(list, category) in categorizedAnime"
        :key="category"
        class="shelf"
      >
        <h2 class="shelf-title">{{ formatCategoryName(category) }}</h2>
        <div class="shelf-scroll">
          <div
            v-for="anime in list"
            :key="anime.id"
            class="anime-card"
          >
            <img
              :src="anime.imageUrl?.trim() || anime.fallbackImage || 'https://placehold.co/160x220?text=No+Image'"
              alt="Anime Image"
            />
            <p>{{ anime.title }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, computed, defineProps } from 'vue'

// ...省略（以前のロジックのまま）

</script>
.loading {
  display: flex;
  flex-direction: column;
  align-items: center;
  color: var(--accent-color);
  font-size: 1.5rem;
  padding: 2rem 0;
}

/* ✅ スピナーアニメーション */
.spinner {
  width: 48px;
  height: 48px;
  border: 6px solid #ccc;
  border-top-color: var(--accent-color, #42b983);
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin-bottom: 1rem;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}





