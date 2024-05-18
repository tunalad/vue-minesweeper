<script setup>
import CellButton from "../components/CellButton.vue";
import { ref } from "vue";

const props = defineProps({
    width: Number,
    height: Number,
    bombs: Number,
});

const emits = defineEmits([
    "gameOver",
    "gameWon",
    "firstClick",
    "flaggedCount",
]);

const board = generateBoard(props.width, props.height, props.bombs);
let boardState = board;
let cellsFlagged = ref(0);

const cellButtons = ref({});

const firstClickMade = ref(false);
const isGameOver = ref(false);

function generateBoard(width, height, mines) {
    let array = new Array(height);

    for (let i = 0; i < array.length; i++) {
        array[i] = new Array(width).fill("X");
    }

    let minesCount = mines;
    // place mines
    while (minesCount > 0) {
        const x = Math.floor(Math.random() * width);
        const y = Math.floor(Math.random() * height);

        if (array[y][x] == "X") {
            array[y][x] = "¤";
            minesCount--;
        }
    }

    return array;
}

function checkNeighbours(x, y) {
    let neighboursCount = 0;

    for (let i = y - 1; i <= y + 1; i++) {
        if (i >= 0 && i < board.length)
            for (let j = x - 1; j <= x + 1; j++) {
                if (j >= 0 && j < board[0].length)
                    if (board[i][j] === "¤") neighboursCount++;
            }
    }

    return neighboursCount;
}

function zeroClick(data) {
    if (data.neighbours === 0) {
        let x = data.x;
        let y = data.y;

        for (let i = y - 1; i <= y + 1; i++) {
            if (i >= 0 && i < board.length) {
                for (let j = x - 1; j <= x + 1; j++) {
                    if (j >= 0 && j < board[0].length && (i !== y || j !== x)) {
                        simClick(j, i);
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

    for (let i = 0; i < props.height; i++) {
        for (let j = 0; j < props.width; j++) {
            if (boardState[i][j] === "¤") simClick(j, i, false, true);
        }
    }
}

function checkLeftCells(x, y) {
    if (!firstClickMade.value) {
        firstClickMade.value = true;
        emits("firstClick");
    }

    boardState[y][x] = "c";
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

        for (let i = 0; i < props.height; i++) {
            for (let j = 0; j < props.width; j++) {
                if (boardState[i][j] === "¤") simClick(j, i, true, false);
            }
        }
    }
}

function countFlagged(isFlagged) {
    cellsFlagged.value += isFlagged ? 1 : -1;
    emits("flaggedCount", cellsFlagged.value);
}
</script>

<template>
    <table :class="{ board: true, 'game-over': isGameOver }">
        <tr class="board-row" v-for="(row, rowI) in board" :key="rowI">
            <CellButton
                v-for="(cell, cellI) in row"
                :key="cellI"
                :cellData="{
                    x: cellI,
                    y: rowI,
                    isBomb: cell,
                    neighbours: checkNeighbours(cellI, rowI),
                }"
                :isGameOver="isGameOver"
                :ref="(el) => (cellButtons[`cell-${cellI}-${rowI}`] = el)"
                @zeroClick="zeroClick"
                @mineClicked="mineClicked"
                @click="checkLeftCells(cellI, rowI)"
                @updateFlagged="countFlagged"
            />
        </tr>
    </table>
</template>

<style scoped>
.board {
    background-color: #ccc;
    padding: 0.25rem;
}

.mine-clicked {
    background-color: red;
}

.game-over {
    pointer-events: none;
}
</style>
