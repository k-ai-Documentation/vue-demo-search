<template lang="pug">
.content
    .logo 
        img(:src="kaiLogo")
    .search-box
        p.text-medium-16.text-white.search-title Make a search 
        p.text-regular-14.text-grey.search-subtitle Type your question and press Enter
        search-bar(:newInput="newInput" @updateInput="handleInputUpdate" @search="searchRequest")
        p.no-answer.text-white.text-regular-14(v-if="searchAnswer && !searchAnswer.answer") No results. Please rephrase or ask another question.

        .loading(v-if="loading")
            p.text-regular-14.text-white Loading {{ loadingProgress }}%
        .search-result.text-regular-14(v-if="searchAnswer && searchAnswer.answer")
            p.answer.text-white.text-regular-14 {{ searchAnswer.answer }}
            .answer-information
                .answer-block
                    p.text-regular-14.text-grey.subtitle Reason
                    p.text-white {{ searchAnswer.reason }}
                .answer-block
                    p.text-regular-14.text-grey.subtitle Sources
                    p.text-white(v-for="source in searchAnswer.documents")
                        a(:href="source.url" target="_blank") {{ source.name }}
                .answer-block
                    p.text-regular-14.text-grey.subtitle Related-questions 
                    p.text-white.clickable(v-for="(question, index) in searchAnswer.followingQuestions" :key="index" @click="searchNewQuestion(question)") {{ question }}
</template>

<script setup lang="ts">
import { ref, type Ref } from "vue";
import SearchBar from './SearchBar.vue';
import type { SearchResult } from "kaistudio-sdk-js/modules/Search";
import { KaiStudio } from "kaistudio-sdk-js"
import kaiLogo from "kai-assets/logo.svg"

const newInput = ref('');
const searchInput = ref('');
const loadingProgress = ref(0);
const searchAnswer: Ref<SearchResult | null> = ref(null)
const loading: Ref<boolean> = ref(false)
const timeoutFlag: Ref<boolean> = ref(false);
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
    timeoutFlag.value = false;
    try {
        if (searchInput.value) {
            searchAnswer.value = null;
            loading.value = true;
            loadingProgress.value = 0;
            // simulate loading
            interval = setInterval(() => {
                if (loadingProgress.value < 90) {
                    loadingProgress.value += 10;
                }
            }, 1000);

            // Start the 20 seconds timeout
            const timeout = setTimeout(() => {
                if (!timeoutFlag.value) { 
                    timeoutFlag.value = true;
                    loading.value = false;
                    searchAnswer.value = {answer:''};
                }
            }, 20000); // 20 seconds
            const request = await kaiSearch.search().query(searchInput.value, "");
            if(!timeoutFlag.value) {
                loadingProgress.value = 100;
                await new Promise(resolve => setTimeout(resolve, 500)); 
                searchAnswer.value = request;
            }
            clearTimeout(timeout); // Clear the timeout as response has been received
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

    .logo {
        width: 133px;
        height: fit-content;
        margin-top: 100px;
        margin-bottom: 56px;
        filter: var(--svg-filter-white-color);
    }
    .search-box {
        width: 799px;
        height: auto;
        background-color: var(--dark-grey-color);
        border: 1px solid var(--color-border);
        border-radius: 12px;
        padding: 11px 11px 30px 11px;

        .search-title {
            margin-bottom: 7px;
        }

        .search-subtitle {
            margin-bottom: 18px;
        }

        .loading {
            margin-top: 8px;
        }
        .no-answer {
            margin-top: 8px
        }

        .search-result {
            width: 622px;
            margin-top: 23px;
            border-radius: 5px;
            text-align: left;
            .answer {
                word-wrap: break-word;
            }
            .answer-information {
                margin-top: 30px;
                .answer-block {
                    margin-top: 15px;
                    .subtitle {
                        margin-bottom: 8px;
                    }
                }

                .clickable {
                    cursor: pointer;
                }
            }
        }
    }
    
}
</style>