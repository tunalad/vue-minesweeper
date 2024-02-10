<script setup>
import { ref } from "vue";

const props = defineProps({
    cellData: Object,
});
const emits = defineEmits(["zeroClick"]);

const clicked = ref(false);
const flagged = ref(false);

function printData() {
    console.log(props.cellData);
    clicked.value = true;
    emits("zeroClick", props.cellData);
}

function simClick() {
    clicked.value = true;
}

function flagButton() {
    console.log("AAAA");
    flagged.value = !flagged.value;
}

defineExpose({ simClick, printData });
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
    <td v-else-if="clicked && props.cellData.isBomb === '¤'">💣</td>
    <!-- no bomb -->
    <td v-else>
        {{ props.cellData.neighbours }}
    </td>
</template>

<style scoped>
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
