<template>
  <div class="anime-shelves">
    <!-- ローディング中 -->
    <div v-if="isLoading" class="loading">
      <div class="spinner"></div>
      <p>アニメ一覧を取得中...</p>
    </div>

    <!-- カテゴリ別にアニメを表示 -->
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
              :src="anime.fallbackImage || 'https://placehold.co/160x220?text=No+Image'"
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

const props = defineProps({
  animeListLocal: {
    type: Array,
    default: () => []
  }
})

const animeListWithFallback = ref([])
const isLoading = ref(true)

// Google Books APIから画像取得
const getImageFromGoogleBooks = async (title) => {
  const url = `https://www.googleapis.com/books/v1/volumes?q=intitle:${encodeURIComponent(title)}+subject:comic&langRestrict=ja&printType=books&orderBy=relevance&maxResults=1`
  try {
    const res = await fetch(url)
    const data = await res.json()
    // itemsがあるかをチェック
    if (!data.items || data.items.length === 0) {
      console.warn(`Google Books API: itemsが見つかりませんでした（${title}）`)
      return ''
    }
    const book = data.items[0]
    const image = book.volumeInfo.imageLinks?.thumbnail || ''
    return image
  } catch (err) {
    console.error(`Google Books画像取得エラー（${title}）:`, err)
    return ''
  }
}


// animeListLocal が更新されたときに fallbackImage を取得
watch(() => props.animeListLocal, async (newList) => {
  isLoading.value = true
  animeListWithFallback.value = await Promise.all(
    newList.map(async (anime) => {
      const fallbackImage = await getImageFromGoogleBooks(anime.title)
      return { ...anime, fallbackImage }
    })
  )
  isLoading.value = false
}, { immediate: true })

// カテゴリ別に分類
const categorizedAnime = computed(() => {
  const groups = {}
  for (const anime of animeListWithFallback.value) {
    const category = anime.review?.WhichList || 'uncategorized'
    if (!groups[category]) groups[category] = []
    groups[category].push(anime)
  }
  return groups
})

// カテゴリ名の整形
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





