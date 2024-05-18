<script setup>
import Board from "./components/Board.vue";
import Timer from "./components/Timer.vue";
import { ref } from "vue";

const boardKey = ref(0);
const smiley = ref(null);
const timerRef = ref(null);
const flaggedCount = ref(0);

const height = ref(9);
const width = ref(9);
const bombs = ref(10);

const customDif = ref(false);
const customDifBoard = ref({
    height: 3,
    width: 3,
    bombs: 1,
});

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

function setDifficulty(board) {
    height.value = parseInt(board.height);
    width.value = parseInt(board.width);
    bombs.value = parseInt(board.bombs);
    resetBoard();
}
</script>

<template>
    <div class="difficulty">
        <a
            href="#"
            @click="
                setDifficulty({ height: 9, width: 9, bombs: 10 });
                customDif = false;
            "
            >Easy</a
        >
        <a
            href="#"
            @click="
                setDifficulty({ height: 16, width: 16, bombs: 40 });
                customDif = false;
            "
            >Medium</a
        >
        <a
            href="#"
            @click="
                setDifficulty({ height: 16, width: 30, bombs: 99 });
                customDif = false;
            "
            >Hard</a
        >
        <a
            href="#"
            @click="
                customDif = true;
                resetBoard();
            "
            >Custom</a
        >
    </div>
    <div class="custom-dif" v-if="customDif">
        <form>
            <span>Height: </span>
            <input v-model="customDifBoard.height" name="height" />
            <br />
            <span>Width: </span>
            <input v-model="customDifBoard.width" name="width" />
            <br />
            <span>Mines: </span>
            <input v-model="customDifBoard.bombs" name="bombs" />
            <br />
            <button
                type="button"
                @click="
                    setDifficulty({
                        height: customDifBoard.height,
                        width: customDifBoard.width,
                        bombs: customDifBoard.bombs,
                    })
                "
            >
                Update
            </button>
        </form>
    </div>
    <div class="stats">
        <div class="flags">
            {{ String(bombs - flaggedCount).padStart(3, "0") }}
        </div>
        <button ref="smiley" class="smiley" @click="resetBoard">🙂</button>
        <div class="time"><Timer ref="timerRef" /></div>
    </div>
    <div class="board-wrapper">
        <Board
            :width="width"
            :height="height"
            :bombs="bombs"
            :key="boardKey"
            @gameOver="gameOver"
            @gameWon="gameWon"
            @firstClick="timerRef.startTimer()"
            @flaggedCount="flaggedCount = $event"
        />
    </div>
</template>

<style scoped>
.difficulty {
    display: flex;
    justify-content: space-evenly;
}
.difficulty a {
    padding: 0 0.25rem;
}

.board-wrapper {
    display: flex;
    justify-content: center;
}

.board {
    border-radius: 0.25rem;
}
.stats {
    display: flex;
    justify-content: space-between;
    align-items: center;
    position: relative;
    height: 2.5rem;
    border-radius: 0.25rem;
    background-color: #ccc;
    padding: 0 1rem;
    margin: 0.5rem 0;
    color: #242424;
}
.smiley {
    width: 2rem;
    height: 2rem;
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
}
.custom-dif input {
    max-width: 3rem;
    max-height: 3rem;
}
</style>
