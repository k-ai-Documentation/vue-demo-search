<template lang="pug">
.content
    .logo 
        img(:src="kaiLogo")
    SearchBox
</template>

<script setup lang="ts">
import SearchBox from './components/SearchBox.vue';
import 'kai-assets/styles/main.scss';
import kaiLogo from "kai-assets/logo.svg"

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
}
</style>

