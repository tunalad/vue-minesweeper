<script setup>
import CellButton from "../components/CellButton.vue";
import { ref } from "vue";

const props = defineProps({
    width: Number,
    height: Number,
    bombs: Number,
});

const board = generateBoard(props.width, props.height, props.bombs);

const cellButtons = ref({});

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
                    if (j >= 0 && j < board.length && (i !== x || j !== y))
                        simClick(i, j);
                }
            }
        }
    }
}

function simClick(x, y) {
    const key = `cell-${x}-${y}`;
    if (cellButtons.value[key]) {
        cellButtons.value[key].simClick();

        cellButtons.value[key] = null;
        zeroClick({ x: x, y: y, neighbours: checkNeighbours(x, y) });
    }
}
</script>

<template>
    <div class="stats">
        <div class="flags">000</div>
        <button class="smiley">🙂</button>
        <div class="time">000</div>
    </div>
    <table class="board">
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
                :ref="(el) => (cellButtons[`cell-${rowI}-${cellI}`] = el)"
                @zeroClick="zeroClick"
            />
        </tr>
    </table>
</template>

<style scoped>
.stats {
    display: flex;
    justify-content: space-between;
}

.board {
    background-color: #ccc;
}
</style>
