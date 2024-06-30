<script setup>
import { ref, computed, nextTick, watch, onMounted } from 'vue'
const boxRef = ref()
const boxNum = ref(1)
const animationKey = ref(Date.now());


const radioText = ref('All')
watch(radioText, () => {
    if (radioText.value === 'Random') {
        random();
    } else {
        changeBoxNum(1);
    }
})

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

const setRandomAnimation = () => {
    // 获取所有的 box 元素
    const boxes = boxRef.value

    const classListMap = {
        1: 'pseudo-1x1',
        3: 'pseudo-3x3',
        5: 'pseudo-5x5',
        10: 'pseudo-10x10'
    };

    const currentClass = classListMap[boxNum.value];
    console.log(currentClass)
    
    // 使用 requestAnimationFrame 重新繪製動畫時，清除所有 box 的偽元素样式
    requestAnimationFrame(() => {
        boxes.forEach(box => {
            box.classList.remove('pseudo-3x3', 'pseudo-5x5', 'pseudo-10x10');
        });
    })


    // 随幾選择部分 box 添加動畫样式，建立 Set 集合，確保內容不會重複
    const randomBoxes = new Set();
    while (randomBoxes.size < boxNum.value) {
        randomBoxes.add(Math.floor(Math.random() * (boxNum.value * boxNum.value)));
    }

    requestAnimationFrame(() => 
        randomBoxes.forEach((box, index) => {
            boxes[index].classList.add(currentClass);
        })
    )
};

const random = () => {
    const boxOptions = [3, 5, 10];
    // 隨機產生 1x1 或 3x3 或 5x5 或 10x10 的方格
    const randomBox = boxOptions[Math.floor(Math.random() * boxOptions.length)];
    changeBoxNum(randomBox);
    nextTick(() => {
        setRandomAnimation();
    });
}
</script>

<template>
    <div class="radio-group">
        <br>
        <div>
            <input v-model="radioText" type="radio" name="radio" id="all" value="All">
            <label for="all" class="radio-label">All</label>
        </div>
        <div>
            <input v-model="radioText" type="radio" name="radio" id="random" value="Random">
            <label for="random" class="radio-label">Random</label>
        </div>
    </div>
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