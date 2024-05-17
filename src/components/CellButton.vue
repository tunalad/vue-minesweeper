<script setup>
import { ref, watch } from "vue";

const props = defineProps({
    cellData: Object,
    isGameOver: Boolean,
});
const emits = defineEmits(["zeroClick", "mineClicked", "updateFlagged"]);

const clicked = ref(false);
const flagged = ref(false);
const revealed = ref(false);

function printData() {
    clicked.value = true;
    if (props.cellData.isBomb !== "¤") emits("zeroClick", props.cellData);
    else emits("mineClicked", props.cellData);
}

function simClick(reveal = false) {
    if (reveal) {
        revealed.value = true;
    } else clicked.value = true;
}

function simFlag() {
    flagged.value = true;
}

function flagButton() {
    flagged.value = !flagged.value;
}

watch(flagged, (newValue) => {
    emits("updateFlagged", newValue);
});

defineExpose({ simClick, printData, simFlag });
</script>

<template>
    <!-- not flagged button -->
    <td v-if="!clicked && !flagged && !revealed">
        <button @click="printData" @contextmenu.prevent="flagButton"></button>
    </td>
    <!-- flagged button -->
    <td v-else-if="!clicked && flagged">
        <button
            @click="none"
            @contextmenu.prevent="flagButton"
            class="disabled"
            :style="
                props.cellData.isBomb !== '¤' && isGameOver
                    ? { backgroundColor: '#722F37' }
                    : {}
            "
        >
            🚩
        </button>
    </td>
    <!-- bomb bombed -->
    <td
        v-else-if="clicked && props.cellData.isBomb === '¤'"
        style="background-color: #722f37"
    >
        <p>💣</p>
    </td>
    <!-- bomb revealed -->
    <td v-else-if="revealed && props.cellData.isBomb === '¤'">
        <button>💣</button>
    </td>
    <!-- flag wrong revealed -->
    <td v-else-if="revealed && flagged && props.cellData.isBomb !== '¤'">
        <button>🚩</button>
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
    line-height: 100%;
    --cell-size: 2rem;
    min-width: var(--cell-size);
    min-height: var(--cell-size);
    max-width: var(--cell-size);
    max-height: var(--cell-size);
    line-height: var(--cell-size);
}

td,
button {
    --cell-size: 2rem;
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
    --cell-size: 2rem;
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
