<template>
  <!--AnimeList.vue-->
  <div>
    <h2>ユーザーのアニメリスト</h2>
    <div v-if="animeListLocal.length === 0">アニメが見つかりません。</div>
    <div v-else class="anime-grid">
      <div v-for="anime in animeListLocal" :key="anime.id" class="anime-card">
        <img :src="anime.imageUrl || anime.fallbackImage || '/no-image.jpg'"  />
        <p>{{ anime.title }}</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  animeList: {
    type: Array,
    required: true
  }
})

const animeListLocal = ref([])
const SERP_API_KEY = import.meta.env.VITE_SERP_API_KEY // 環境変数名修正済み

// SerpAPIで画像を取得
const getImageFromSerpApi = async (title) => {
  const query = `${title} アニメ`
  const url = `https://serpapi.com/search.json?q=${encodeURIComponent(query)}&tbm=isch&api_key=${SERP_API_KEY}`
  try {
    const res = await fetch(url)
    const data = await res.json()
    return data.images_results?.[0]?.original || ''
  } catch (error) {
    console.error('SerpAPI画像取得エラー:', error)
    return ''
  }
}

// Google Books APIから漫画表紙を取得
const getImageFromGoogleBooks = async (title) => {
  const query = `${title}`
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

// 両方試す（SerpAPI → Google Books）
const getFallbackImage = async (title) => {
  const serpImage = await getImageFromSerpApi(title)
  if (serpImage) return serpImage

  const googleBooksImage = await getImageFromGoogleBooks(title)
  return googleBooksImage
}

// animeList 更新時にfallbackImage取得
watch(() => props.animeList, async (newList) => {
  animeListLocal.value = await Promise.all(
    newList.map(async anime => {
      if (!anime.imageUrl) {
        const fallbackImage = await getFallbackImage(anime.title)
        return { ...anime, fallbackImage }
      } else {
        return anime
      }
    })
  )
}, { immediate: true })

</script>
