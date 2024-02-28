<script setup>
import Board from "./components/Board.vue";
import Timer from "./components/Timer.vue";
import { ref } from "vue";

const boardKey = ref(0);
const smiley = ref(null);
const timerRef = ref(null);

function resetBoard() {
    smiley.value.innerText = "🙂";
    boardKey.value++;
    timerRef.value.resetTimer();
}

function gameOver() {
    smiley.value.innerText = "😵";
    timerRef.value.stopTimer();
}

function gameWon() {
    smiley.value.innerText = "😎";
    timerRef.value.stopTimer();
}
</script>

<template>
    <div class="stats">
        <div class="flags">000</div>
        <button ref="smiley" class="smiley" @click="resetBoard">🙂</button>
        <div class="time"><Timer ref="timerRef" /></div>
    </div>
    <Board
        :width="10"
        :height="10"
        :bombs="10"
        :key="boardKey"
        @gameOver="gameOver"
        @gameWon="gameWon"
        @firstClick="timerRef.startTimer()"
    />
</template>

<style scoped>
.stats {
    display: flex;
    justify-content: space-between;
}
</style>
