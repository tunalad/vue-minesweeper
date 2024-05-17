<script setup>
import Board from "./components/Board.vue";
import Timer from "./components/Timer.vue";
import { ref } from "vue";

const boardKey = ref(0);
const smiley = ref(null);
const timerRef = ref(null);
const flaggedCount = ref(0);
const bombs = ref(10);

function resetBoard() {
    smiley.value.innerText = "🙂";
    boardKey.value++;
    timerRef.value.resetTimer();
    flaggedCount.value = 0;
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
        <div class="flags">
            {{ String(bombs - flaggedCount).padStart(3, "0") }}
        </div>
        <button ref="smiley" class="smiley" @click="resetBoard">🙂</button>
        <div class="time"><Timer ref="timerRef" /></div>
    </div>
    <Board
        :width="10"
        :height="10"
        :bombs="bombs"
        :key="boardKey"
        @gameOver="gameOver"
        @gameWon="gameWon"
        @firstClick="timerRef.startTimer()"
        @flaggedCount="flaggedCount = $event"
    />
</template>

<style scoped>
.stats {
    display: flex;
    justify-content: space-between;
}
</style>
