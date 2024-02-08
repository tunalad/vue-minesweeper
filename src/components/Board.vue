<script setup>
const props = defineProps({
    width: Number,
    height: Number,
    bombs: Number,
});

const board = generateBoard(props.width, props.height);

function generateBoard(width, height, mines) {
    let array = new Array(width);

    for (let i = 0; i < array.length; i++) {
        array[i] = new Array(height).fill("X");
    }

    return array;
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
            <td class="board-cell" v-for="(cell, cellI) in row" :key="cellI">
                {{ cell }}
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
