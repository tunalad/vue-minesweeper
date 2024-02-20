<script setup>
import Board from "./components/Board.vue";
import { ref } from "vue";

const boardKey = ref(0);
const smiley = ref(null);

function resetBoard() {
    smiley.value.innerText = "🙂";
    boardKey.value++;
}

function gameOver() {
    smiley.value.innerText = "😵";
}

function gameWon() {
    smiley.value.innerText = "😎";
}
</script>

<template>
    <div class="stats">
        <div class="flags">000</div>
        <button ref="smiley" class="smiley" @click="resetBoard">🙂</button>
        <div class="time">000</div>
    </div>
    <Board
        :width="10"
        :height="10"
        :bombs="10"
        :key="boardKey"
        @gameOver="gameOver"
        @gameWon="gameWon"
    />
</template>

<style scoped>
.stats {
    display: flex;
    justify-content: space-between;
}
</style>
