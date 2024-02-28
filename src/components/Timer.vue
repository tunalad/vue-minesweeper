<script setup>
import { ref, watchEffect } from "vue";

const timerTime = ref(0);
const timerRunning = ref(false);

function startTimer() {
    timerRunning.value = true;
    console.log("timer started");
}

function stopTimer() {
    timerRunning.value = false;
    console.log("timer stopped");
}

function resetTimer() {
    stopTimer();
    timerTime.value = 0;
    console.log("timer reset");
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
    <div>{{ timerTime }}</div>
</template>
