<template lang="pug">
.content
    .logo 
        img(src="@/assets/logo.svg")
    search-bar(:newInput="newInput" @updateInput="handleInputUpdate" @search="searchRequest")
    .loading(v-if="loading")
        p Loading {{ loadingProgress }}%
    .search-result(v-if="searchAnswer")
        p.answer {{ searchAnswer.answer }}
        .answer-block
            p.answer-title Reason
            p.answer-content {{ searchAnswer.reason }}
        .answer-block
            p.answer-title Sources
            p.answer-content(v-for="source in searchAnswer.documents")
                a(:href="source.url" target="_blank") {{ source.name }}
        .answer-block
            p.answer-title Related-questions 
            p.answer-content.clickable(v-for="(question, index) in searchAnswer.followingQuestions" :key="index" @click="searchNewQuestion(question)") {{ question }}
</template>

<script setup lang="ts">
import { ref, type Ref } from "vue";
import SearchBar from './SearchBar.vue';
import type { SearchResult } from "kaistudio-sdk-js/modules/Search";
import { KaiStudio } from "kaistudio-sdk-js"

const newInput = ref('');
const searchInput = ref('');
const loadingProgress = ref(0);
const searchAnswer: Ref<SearchResult | null> = ref(null)
const loading: Ref<boolean> = ref(false)
const kaiSearch = new KaiStudio({ organizationId: process.env.VUE_APP_ORGANIZATION_ID, instanceId: process.env.VUE_APP_INSTANCE_ID, apiKey: process.env.VUE_APP_API_KEY })

const handleInputUpdate = (newInput: string) => {
    searchInput.value = newInput;
};

const searchNewQuestion = (question: string) => {
    newInput.value = question
    searchInput.value = question
    searchRequest();
}

async function searchRequest() {
    let interval: number | undefined; 
    try {
        if (searchInput.value) {
            searchAnswer.value = null;
            loading.value = true;
            loadingProgress.value = 0;
            // simulate loading
            interval = setInterval(() => {
                if (loadingProgress.value < 90) {
                    loadingProgress.value += 10;
                } else if (loadingProgress.value >= 90 && loadingProgress.value < 100) {
                    loadingProgress.value = 100;
                }
            }, 1000);

            const request = await kaiSearch.search().query(searchInput.value, "");
            await new Promise(resolve => setTimeout(resolve, 500)); 
            searchAnswer.value = request;
        }
    } catch (e) {
        console.error(e);
    } finally {
        if (interval !== undefined) clearInterval(interval);
        loading.value = false;
    }
}

</script>


<style lang="scss">
.content {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
    height: 100%;
    background: white;
    font-family: 'Lato', sans-serif;

    .logo {
        width: 133px;
        height: fit-content;
        margin-top: 100px;
        margin-bottom: 56px;
    }

    .search-result {
        width: 540px;
        margin-top: 42px;
        border-radius: 5px;
        padding: 0px 0px 0px 20px;
        font-size: 13px;
        text-align: left;

        .answer {
            word-wrap: break-word;
            color: black;
            margin-bottom: 25px;
        }

        .answer-block {
            margin-top: 21px;
        }

        .answer-title {
            font-weight: 600;
            color: #B7B7B9;
        }

        .answer-content {
            font-weight: normal;
            color: #555B6F;
        }

        .clickable {
            cursor: pointer;
        }
    }
}
</style>