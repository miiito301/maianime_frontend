<template>
<!--ResultForm.vue-->
        <form @submit.prevent="submitReview" >
                <input v-model="name" type="text" placeholder="Enter your name" required />
                <select v-model="WhichList"> 
                    <option value="dropped">Dropped</option>
                    <option value="watched">Watched</option>
                    <option value="bests">My bests</option>
                    <option value="watching">Watching</option>
                    <option value="gotta_watch">Gotta Watch</option>
                </select>

            <!-- 送信ボタン -->
                <button type="submit">
                Save</button>
            
        </form>

</template>

<script setup>

    import { ref} from 'vue'

    // props: 親から渡されたアニメ情報
    const props = defineProps({
    anime: Object
    })

    // emit: 親にレビューを送信
    const emit = defineEmits(['submit-review'])

    // レビュー入力用データ
    const name = ref('')
    const WhichList = ref('watched') // 初期値は 'watched'


    // フォーム送信
    const submitReview = () => { //本の情報と感想情報をresult.vueに送信
    const reviewData = {
        id: props.anime.id,
        title: props.anime.title,
        image: props.anime.images.recommended_url,
        review: {
        name: name.value,
        WhichList: WhichList.value
        }
    }

    console.log("送信内容:", reviewData);

    emit('submit-review', reviewData)
    name.value = ''     // フォームを初期化
    WhichList.value = 'watched' // 初期値に戻す
    }

</script>