<template lang="pug">
.content
    .logo 
        img(:src="kaiLogo")
    .btn-group
        button.btn-icon(@click="showSearchBox = true") search
        button.btn-icon(@click="showSearchBox = false") search with identify documents
    SearchBox(v-if="showSearchBox")
    SearchChat(v-else)
</template>

<script setup lang="ts">
import { ref, type Ref } from 'vue';
import SearchBox from './components/SearchBox.vue';
import SearchChat from './components/SearchChat.vue';
import 'kai-assets/styles/main.scss';
import kaiLogo from "kai-assets/logo.svg"

const showSearchBox = ref(true);


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

    .btn-group {
        display: flex;
        flex-direction: row;
        gap: 16px;
        margin-bottom: 10px;
    }
}
</style>

