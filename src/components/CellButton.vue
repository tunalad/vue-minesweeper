<script setup>
import { ref } from "vue";

const props = defineProps({
    cellData: Object,
});
const emits = defineEmits(["zeroClick", "mineClicked"]);

const clicked = ref(false);
const flagged = ref(false);

function printData() {
    clicked.value = true;
    if (props.cellData.isBomb !== "¤") emits("zeroClick", props.cellData);
    else emits("mineClicked", props.cellData);
}

function simClick() {
    clicked.value = true;
}

function simFlag() {
    flagged.value = true;
}

function flagButton() {
    flagged.value = !flagged.value;
}

defineExpose({ simClick, printData, simFlag });
</script>

<template>
    <!-- not flagged button -->
    <td v-if="!clicked && !flagged">
        <button @click="printData" @contextmenu.prevent="flagButton"></button>
    </td>
    <!-- flagged button -->
    <td v-else-if="!clicked && flagged">
        <button
            @click="none"
            @contextmenu.prevent="flagButton"
            class="disabled"
        >
            🚩
        </button>
    </td>
    <!-- bomb -->
    <td
        v-else-if="clicked && props.cellData.isBomb === '¤'"
        style="background-color: red"
    >
        <p>💣</p>
    </td>
    <!-- no bomb -->
    <td v-else>
        <p @click.prevent @contextmenu.prevent>
            {{ props.cellData.neighbours }}
        </p>
    </td>
</template>

<style scoped>
p {
    margin: 0;
    padding: 0;
}

td,
button {
    --cell-size: 25px;
    min-width: var(--cell-size);
    min-height: var(--cell-size);
    max-width: var(--cell-size);
    max-height: var(--cell-size);
    line-height: var(--cell-size);

    color: #242424;
    text-align: center;
    vertical-align: middle;
    padding: 0;
    margin: 0;
    box-sizing: border-box;
}

button {
    --cell-size: 25px;
    min-width: var(--cell-size);
    min-height: var(--cell-size);
    max-width: var(--cell-size);
    max-height: var(--cell-size);
    line-height: var(--cell-size);
}

button.disabled {
    transition: none;
}
</style>
