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

const props = defineProps({
  animeListLocal: {
    type: Array,
    default: () => []
  }
})

const animeListWithFallback = ref([])
const isLoading = ref(true) // ✅ 追加

// ✅ Google Books APIから画像取得
const getImageFromGoogleBooks = async (title) => {
  const queries = [
    `${title} 漫画`,
    `${title} コミック`,
    `${title} アニメ`,
    `${title}`,
    `${title} コミカライズ`,
  ]

  for (const query of queries) {
    const url = `https://www.googleapis.com/books/v1/volumes?q=intitle:${encodeURIComponent(query)}+subject:comic&langRestrict=ja&printType=books&orderBy=relevance&maxResults=1`
    try {
      const res = await fetch(url)
      const data = await res.json()
      const book = data.items?.[0]
      const image = book?.volumeInfo?.imageLinks?.thumbnail?.replace('http://', 'https://')
      if (image) {
        return image
      }
    } catch (err) {
      console.error(`Google Books画像取得エラー（${query}）:`, err)
    }
  }

  return ''
}

// ✅ animeListLocal が更新されたときに fallbackImage を追加
watch(() => props.animeListLocal, async (newList) => {
  isLoading.value = true // 開始時ローディング
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
  isLoading.value = false // 読み込み完了
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





