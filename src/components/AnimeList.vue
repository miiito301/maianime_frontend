<template>
  <div class="anime-shelves">
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
            :src="anime.imageUrl || anime.fallbackImage || '/no-image.jpg'"
            alt="Anime Image"
          />
          <p>{{ anime.title }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { defineProps } from 'vue'

const props = defineProps({
  animeListLocal: {
    type: Array,
    required: true,
    default: () => []
  }
})

// anime.review.WhichList でグループ化
const categorizedAnime = computed(() => {
  const groups = {}
  const list = props.animeListLocal ?? []
  for (const anime of list) {
    const category = anime.review?.WhichList || 'uncategorized'
    if (!groups[category]) {
      groups[category] = []
    }
    groups[category].push(anime)
  }
  return groups
})

const formatCategoryName = (key) => {
  const names = {
    watched: 'Watched',
    watching: 'Watching',
    dropped: 'Dropped',
    bests: 'My Bests',
    gotta_watch: 'Gotta Watch',
    uncategorized: 'Uncategorized'
  }
  return names[key] || key
}
</script>




