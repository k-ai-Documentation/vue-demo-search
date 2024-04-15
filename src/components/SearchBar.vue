<template lang="pug">
.search-bar 
    input(type="text" placeholder="Search for anything" v-model="searchInput" @input="updateInput" @keyup.enter="emitSearch" @focus="isClicked=true" @blur="isClicked=false")
    img(src="@/assets/search.svg" @click="emitSearch" :class="isClicked || searchInput ? 'left' : 'right'")
    img.icon-close(v-if="searchInput" src="@/assets/close-small.svg" @click="clearSearch")
</template>

<script setup lang="ts">
import { defineEmits, ref, defineProps, watch } from 'vue';

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
    border-radius: 80px;
    width: 515px;
    height: 49px;
    border: 1px solid #555b6f;
    input {
        outline: none;
        border: none;
        background: transparent;
        min-width: 100px;
        display: inline-block;
        width: 100%;
        padding: 0 14px 0 42px;
        height: 49px;
        font-size: 14px;
        font-weight: bold;
        color: black;
        overflow: hidden;
        text-overflow: ellipsis;
        width: 432px;
        &::placeholder {
            font-weight: normal;
            font-size: 14px;
        }
    }
    .left {
        position: absolute;
        left: 14px;
        height: 22px;
        cursor: pointer;
    }
    .right {
        position: absolute;
        right: 25px;
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
