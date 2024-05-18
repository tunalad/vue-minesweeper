<script setup>
import Board from "./components/Board.vue";
import Timer from "./components/Timer.vue";
import { ref, reactive } from "vue";

const boardKey = ref(0);
const smiley = ref(null);
const timerRef = ref(null);
const flaggedCount = ref(0);

const height = ref(9);
const width = ref(9);
const bombs = ref(10);

const difficulty = ref(0); // 0 (easy) - 3 (custom)

const customDifBoard = reactive({
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

function setDifficulty(boardCustom) {
    let maxMines = 999

    if(boardCustom.height < 3)
        boardCustom.height = 3;
    if(boardCustom.width < 3)
        boardCustom.width = 3;
    if(boardCustom.bombs < 2)
        boardCustom.bombs = 2;

    maxMines = Math.floor((boardCustom.height * boardCustom.width) / Math.PI);

    if (maxMines < boardCustom.bombs) {
        boardCustom.bombs = maxMines;
        customDifBoard.bombs = maxMines;
    }

    height.value = parseInt(boardCustom.height);
    width.value = parseInt(boardCustom.width);
    bombs.value = parseInt(boardCustom.bombs);

    resetBoard();
}

</script>

<template>
    <div class="difficulty">
        <a
            href="#"
            :class="{ 'selection-indicator': difficulty === 0 }"
            @click="
                setDifficulty({ height: 9, width: 9, bombs: 10 });
                difficulty = 0;
            "
            >Easy</a
        >
        <a
            href="#"
            :class="{ 'selection-indicator': difficulty === 1 }"
            @click="
                setDifficulty({ height: 16, width: 16, bombs: 40 });
                difficulty = 1;
            "
            >Medium</a
        >
        <a
            href="#"
            :class="{ 'selection-indicator': difficulty === 2 }"
            @click="
                setDifficulty({ height: 16, width: 30, bombs: 99 });
                difficulty = 2;
            "
            >Hard</a
        >
        <a
            href="#"
            :class="{ 'selection-indicator': difficulty === 3 }"
            @click="
                difficulty = 3;
                resetBoard();
            "
            >Custom</a
        >
    </div>
    <div class="custom-dif" v-if="difficulty === 3">
        <form>
            <span>Height: </span>
            <input v-model="customDifBoard.height" name="height" />
            <span>Width: </span>
            <input v-model="customDifBoard.width" name="width" />
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
.difficulty a {
    padding: 0 0.5rem;
}
.custom-dif input {
    padding-right: 0.5rem;
}
.custom-dif span {
    padding-left: 0.5rem;
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

.selection-indicator {
    text-decoration: underline;
}
</style>
