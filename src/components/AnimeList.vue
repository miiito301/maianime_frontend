<template>
  <!--<AnimeList.vue-->
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
            :src="anime.fallbackImage || anime.imageUrl?.trim() || 'https://placehold.co/160x220?text=No+Image'"
            alt="Anime Image"
          />
          <p>{{ anime.title }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, computed, defineProps } from 'vue'

const props = defineProps({
  animeListLocal: {
    type: Array,
    default: () => []
  }
})

const animeListWithFallback = ref([])

// ✅ Google Books APIから画像取得
const getImageFromGoogleBooks = async (title) => {
  const query = `${title} 漫画`
  const url = `https://www.googleapis.com/books/v1/volumes?q=${encodeURIComponent(query)}`
  try {
    const res = await fetch(url)
    const data = await res.json()
    const book = data.items?.[0]
    return book?.volumeInfo?.imageLinks?.thumbnail?.replace('http://', 'https://') || ''
  } catch (err) {
    console.error('Google Books画像取得エラー:', err)
    return ''
  }
}

// ✅ animeListLocal が更新されたときに fallbackImage を追加
watch(() => props.animeListLocal, async (newList) => {
  animeListWithFallback.value = await Promise.all(
    newList.map(async (anime) => {
      if (!anime.imageUrl?.trim()) {
        const fallbackImage = await getImageFromGoogleBooks(anime.title)
        return { ...anime, fallbackImage }
      } else {
        return anime
      }
    })
  )
}, { immediate: true })

// ✅ カテゴリ別に分類
const categorizedAnime = computed(() => {
  const groups = {}
  const list = animeListWithFallback.value ?? []
  for (const anime of list) {
    const category = anime.review?.WhichList
    if (!groups[category]) groups[category] = []
    groups[category].push(anime)
  }
  return groups
})

// ✅ カテゴリ名表示用
const formatCategoryName = (key) => {
  const names = {
    watched: 'Watched',
    watching: 'Watching',
    dropped: 'Dropped',
    bests: 'My Bests',
    gotta_watch: 'Gotta Watch',
  }
  return names[key] || key
}
</script>




