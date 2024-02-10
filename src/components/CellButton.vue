<script setup>
import { ref } from "vue";

const props = defineProps({
    cellData: Object,
});
const emits = defineEmits(["zeroClick"]);

const clicked = ref(false);

function printData() {
    console.log(props.cellData);
    clicked.value = true;
    emits("zeroClick", props.cellData);
}

function simClick() {
    clicked.value = true;
}

defineExpose({ simClick, printData });
</script>

<template>
    <td v-if="!clicked"><button @click="printData"></button></td>
    <td v-else-if="clicked && props.cellData.isBomb === '¤'">💣</td>
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
</style>
