
<template>
  <!--APP.vue-->
  <Form @submit-form="getAnime"/>
  <Result :animeList="animeList"  @submit-review="handleReviewSubmit"/>
  <WhosList @submit-username="getUserAnimeList"/>
  <AnimeList  :animeList="userAnimeList"/>
</template>

<script setup>

import Form from './components/Form.vue'
import Result from './components/Result.vue'
import WhosList from './components/WhosList.vue'
import AnimeList from './components/AnimeList.vue'
import {ref} from "vue"


const animeList=ref([])

const getAnime =(title)=>{
  if (!title)return

  fetch(`https://api.annict.com/v1/works?access_token=${import.meta.env.VITE_ANNICT_TOKEN}&filter_title=${title}`)
    .then(response => response.json())
    .then(data => {
      animeList.value=data.works
    })
    .catch(error => {
      console.error("Error fetching anime data:", error);
    });
}

  // ResultForm から送られたレビューを受け取りバックエンドへ送信

const API_BASE = import.meta.env.VITE_RENDER_URL

    const handleReviewSubmit = async (reviewData) => {
    try { 
        await fetch(`${API_BASE}/api/reviews`, {
        method: "POST",
        headers: {"Content-Type": "application/json"},
        body: JSON.stringify(reviewData)
        })
        alert("感想を保存しました")
    } catch (error) {
        console.error("感想の保存に失敗：", error)
        alert("保存に失敗しました")
    }
  }

// ユーザーのアニメリストを取得する関数
const userAnimeList = ref([])

const getUserAnimeList = async (username) => {
  try {
    const res = await fetch(`/api/user-anime-list?username=${encodeURIComponent(username)}`)
    const data = await res.json()
    userAnimeList.value = data.animeList || []
  } catch (err) {
    console.error("Error fetching user's anime list:", err)
    userAnimeList.value = []
  }
}



</script>