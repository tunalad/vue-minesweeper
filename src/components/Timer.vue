<script setup>
import { ref, watchEffect } from "vue";

const timerTime = ref(0);
const timerRunning = ref(false);

function startTimer() {
    timerRunning.value = true;
}

function stopTimer() {
    timerRunning.value = false;
}

function resetTimer() {
    stopTimer();
    timerTime.value = 0;
}

defineExpose({
    startTimer,
    stopTimer,
    resetTimer,
});

// Timer logic
watchEffect(() => {
    if (timerRunning.value) {
        const intervalId = setInterval(() => {
            timerTime.value++;
        }, 1000);
        // Clear the interval when timer stops
        watchEffect(() => {
            if (!timerRunning.value) {
                clearInterval(intervalId);
            }
        });
    }
});
</script>

<template>
    <div>{{ String(timerTime).padStart(3, "0") }}</div>
</template>
