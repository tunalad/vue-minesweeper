<script setup>
const props = defineProps({
    width: Number,
    height: Number,
    bombs: Number,
});

const board = generateBoard(props.width, props.height, 38);

//board[3][2] = "¤";
//board[7][9] = "¤";
//board[4][5] = "¤";
//board[5][5] = "¤";

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

    console.log(board.length);

    for (let i = x - 1; i <= x + 1; i++) {
        if (i >= 0 && i < board.length)
            for (let j = y - 1; j <= y + 1; j++) {
                if (j >= 0 && j < board.length)
                    if (board[i][j] === "¤") neighboursCount++;
            }
    }

    console.log(`Neighbours count of ${x} ${y} is: ${neighboursCount}`);
    return neighboursCount;
}
</script>

<template>
    <div class="stats">
        <div class="flags">000</div>
        <div class="smiley">🙂</div>
        <div class="time">000</div>
    </div>
    <table class="board">
        <tr class="board-row" v-for="(row, rowI) in board" :key="rowI">
            <td
                class="board-cell"
                v-for="(cell, cellI) in row"
                :key="cellI"
                @click="checkNeighbours(rowI, cellI)"
            >
                <template v-if="cell === '¤'"> 💣 </template>
                <template v-else>
                    {{ checkNeighbours(rowI, cellI) }}
                </template>
            </td>
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

.board-cell {
    --cell-size: 25px;
    min-width: var(--cell-size);
    min-height: var(--cell-size);
    line-height: var(--cell-size);
    text-align: center;
    vertical-align: middle;
    background-color: #242424;
}
</style>
