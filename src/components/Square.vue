<script setup>
import { ref, computed, nextTick, watch, onMounted } from 'vue'
const boxRef = ref()
const boxNum = ref(1)
const animationKey = ref(Date.now());


const boxes = computed(() => (boxNum.value === 1 ? [1] : Array.from({ length: boxNum.value * boxNum.value }, (_, i) => i + 1)));
const changeBoxNum = (box) => {
    boxNum.value = box
    animationKey.value = Date.now(); 
}

const boxStyle = computed(() => {
    const boxSize = 390 / boxNum.value - 8;
    return {
    width: `${boxSize}px`,
    height: `${boxSize}px`,
    };
});

const pseudoStyle = computed(() => `pseudo-${boxNum.value}x${boxNum.value}`);

</script>

<template>

    <div class="buttons">
        <button @click="changeBoxNum(1)">1x1</button>
        <button @click="changeBoxNum(3)">3x3</button>
        <button @click="changeBoxNum(5)">5x5</button>
        <button @click="changeBoxNum(10)">10x10</button>
    </div>
    <div class="container">
        <div
            v-for="(box, index) in boxes" 
            :key="`${animationKey}-${index}`"
            ref="boxRef"
            class="box"
            :style="boxStyle" 
            :class="pseudoStyle"
        />
    </div>
</template>

<style scoped>

.radio-label {
    color: #fff;
}

.radio-group {
    display: flex;
    gap: 8px;
    margin-bottom: 10px;
}

.buttons {
    display: flex;
    gap: 8px;
    margin-bottom: 10px;
}

.buttons > button {
    padding: 4px 8px;
}

.container {
    display: flex;
    flex-wrap: wrap;
    align-content: flex-start;
    justify-content: space-between;
    gap: 8px;
    width: 390px;
    max-width: 390px;
}

.box {
    position: relative;
    width: 390px;
    aspect-ratio: 1 / 1;
    background-color: #787878;
    display: flex;
    justify-content: center;
    align-items: center;
    overflow: hidden;
    border-radius: 8px;
}

.box::before {
    content: "";
    position: absolute;
    width: var(--pseudo-width, 0); /* default width is 0px */
    height: var(--pseudo-height, 0);
    background-color: white;
    animation-delay: 500ms;
    animation: animate 3s ease-in-out infinite;
}

.pseudo-1x1::before {
  --pseudo-width: 200px;
  --pseudo-height: 140%;
}
.pseudo-3x3::before {
  --pseudo-width: 50px;
  --pseudo-height: 140%;
}

.pseudo-5x5::before {
  --pseudo-width: 20px;
  --pseudo-height: 140%;
}

.pseudo-10x10::before {
  --pseudo-width: 10px;
  --pseudo-height: 140%;
}

.box::after {
    content: "";
    position: absolute;
    inset: 2px;
    background: #202020;
    border-radius: 8px;
}
@keyframes animate {
    0% {
        transform: rotate(0deg);
    }
    100% {
        transform: rotate(360deg);
    }
}
</style>