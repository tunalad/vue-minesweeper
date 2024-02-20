<script setup>
import Board from "./components/Board.vue";
import { ref } from "vue";

let boardKey = ref(0);

function resetBoard() {
    boardKey.value++;
}
</script>

<template>
    <div class="stats">
        <div class="flags">000</div>
        <button class="smiley" @click="resetBoard">🙂</button>
        <div class="time">000</div>
    </div>
    <Board :width="10" :height="10" :bombs="10" :key="boardKey" />
</template>

<style scoped>
.stats {
    display: flex;
    justify-content: space-between;
}
</style>
