<template lang="pug">
.search-bar 
    input(type="text" placeholder="Search for anything" v-model="searchInput" @input="updateInput" @keyup.enter="emitSearch" @focus="isClicked=true" @blur="isClicked=false")
    img.icon-18.left(:src="searchIcon" @click="emitSearch")
    img.icon-18.icon-close(v-if="searchInput" :src="closeIcon" @click="clearSearch")
</template>

<script setup lang="ts">
import { defineEmits, ref, defineProps, watch } from 'vue';
import searchIcon from 'kai-assets/icons/search.svg';
import closeIcon from 'kai-assets/icons/close-small.svg';

const props = defineProps({
    newInput: String,
});

const emit = defineEmits(['updateInput', 'search']);
const isClicked = ref(false);
const searchInput = ref('');

const updateInput = (event: { target: { value: any } }) => {
    emit('updateInput', event.target.value);
};

const emitSearch = () => {
    emit('search');
};

const clearSearch = () => {
    searchInput.value = '';
};

watch(
    () => props.newInput,
    (newValue) => {
        props.newInput ? (searchInput.value = props.newInput) : (searchInput.value = '');
    }
);
</script>

<style lang="scss" scoped>
.search-bar {
    display: flex;
    align-items: center;
    justify-content: left;
    position: relative;
    width: 626px;
    height: 30px;
    border-radius: 20px;
    input {
        outline: none;
        border: none;
        background: transparent;
        min-width: 100px;
        display: inline-block;
        width: 100%;
        font-size: 14px;
        color: var(--white-color);
        overflow: hidden;
        text-overflow: ellipsis;
        width: 543px;
    }
    .left {
        position: absolute;
        left: 14px;
        height: 22px;
        cursor: pointer;
    }
    .icon-close {
        position: absolute;
        right: 14px;
        height: 17px;
        cursor: pointer;
    }
}
</style>
