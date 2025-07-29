<template>
  <!--Result.vue-->
  <div>
    <div v-if="animeList.length === 0" class="no-results">No Anime Found</div>
    <div v-else class="result-container">
      <div v-for="anime in animeList" :key="anime.id" class="result-card">
        <p>{{ anime.title }}</p>

                        <!-- Review ボタン -->
                <div>
                    <button
                        @click="toggleReview(anime.id)"
                    >
                        Add On Your List
                    </button>
                </div>

                <transition>
                    <div v-if="showReviewFormId === anime.id">
                    <ResultForm :anime="anime" @submit-review="handleSubmitReview" />
                    </div>
                </transition>


      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  animeList: {
    type: Array,
    required: true,
    default: () => []
  }
})

    import {ref} from "vue"
    import ResultForm from "./ResultForm.vue"

    const showReviewFormId =ref(null) //表示中の感想フォームのID

    const toggleReview = (id) => {
        showReviewFormId.value =showReviewFormId.value === id ? null : id
    }

    const emit = defineEmits(['submit-review'])

    //感想の送信イベントを受け取り、バックエンドに送信
    const handleSubmitReview = (reviewData) => {
        emit('submit-review', reviewData)
    }


</script>
