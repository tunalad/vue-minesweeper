<script setup>
import CellButton from "../components/CellButton.vue";
import { ref } from "vue";

const props = defineProps({
    width: Number,
    height: Number,
    bombs: Number,
});

const emits = defineEmits(["gameOver", "gameWon", "firstClick"]);

const board = generateBoard(props.width, props.height, props.bombs);
let boardState = board;

const cellButtons = ref({});

const firstClickMade = ref(false);
const isGameOver = ref(false);

function generateBoard(width, height, mines) {
    let array = new Array(width);

    for (let i = 0; i < array.length; i++) {
        array[i] = new Array(height).fill("X");
    }

    let minesCount = mines;
    // place mines
    while (minesCount > 0) {
        const x = Math.floor(Math.random() * width);
        const y = Math.floor(Math.random() * height);

        if (array[x][y] == "X") {
            array[x][y] = "¤";
            minesCount--;
        }
    }

    return array;
}

function checkNeighbours(x, y) {
    let neighboursCount = 0;

    for (let i = x - 1; i <= x + 1; i++) {
        if (i >= 0 && i < board.length)
            for (let j = y - 1; j <= y + 1; j++) {
                if (j >= 0 && j < board.length)
                    if (board[i][j] === "¤") neighboursCount++;
            }
    }

    return neighboursCount;
}

function zeroClick(data) {
    if (data.neighbours === 0) {
        let x = data.x;
        let y = data.y;

        for (let i = x - 1; i <= x + 1; i++) {
            if (i >= 0 && i < board.length) {
                for (let j = y - 1; j <= y + 1; j++) {
                    if (j >= 0 && j < board.length && (i !== x || j !== y)) {
                        simClick(i, j);
                        boardState[i][j] = "c";
                    }
                }
            }
        }
    }
}

function simClick(x, y, flagging = false, reveal = false) {
    const key = `cell-${x}-${y}`;
    if (cellButtons.value[key]) {
        if (flagging && !reveal) cellButtons.value[key].simFlag();
        else if (reveal && !flagging) cellButtons.value[key].simClick(true);
        else {
            cellButtons.value[key].simClick();

            cellButtons.value[key] = null;
            zeroClick({ x: x, y: y, neighbours: checkNeighbours(x, y) });
        }
    }
}

function mineClicked(e) {
    emits("gameOver");
    isGameOver.value = true;

    for (let i = 0; i < props.width; i++) {
        for (let j = 0; j < props.height; j++) {
            if (boardState[i][j] === "¤") simClick(i, j, false, true);
        }
    }
}

function checkLeftCells(x, y) {
    if (!firstClickMade.value) {
        firstClickMade.value = true;
        emits("firstClick");
    }

    boardState[x][y] = "c";
    // count non "c" items in this 2d array

    let count_c = 0;
    for (let i = 0; i < board.length; i++) {
        for (let j = 0; j < board[i].length; j++) {
            if (board[i][j] === "c") {
                count_c++;
            }
        }
    }

    if (count_c + props.bombs === props.width * props.height) {
        emits("gameWon");
        isGameOver.value = true;

        for (let i = 0; i < props.width; i++) {
            for (let j = 0; j < props.height; j++) {
                if (boardState[i][j] === "¤") simClick(i, j, true, false);
            }
        }
    }
}
</script>

<template>
    <table :class="{ board: true, 'game-over': isGameOver }">
        <tr class="board-row" v-for="(row, rowI) in board" :key="rowI">
            <CellButton
                v-for="(cell, cellI) in row"
                :key="cellI"
                :cellData="{
                    x: rowI,
                    y: cellI,
                    isBomb: cell,
                    neighbours: checkNeighbours(rowI, cellI),
                }"
                :isGameOver="isGameOver"
                :ref="(el) => (cellButtons[`cell-${rowI}-${cellI}`] = el)"
                @zeroClick="zeroClick"
                @mineClicked="mineClicked"
                @click="checkLeftCells(rowI, cellI)"
            />
        </tr>
    </table>
</template>

<style scoped>
.board {
    background-color: #ccc;
}

.mine-clicked {
    background-color: red;
}

.game-over {
    pointer-events: none;
}
</style>
