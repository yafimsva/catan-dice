<script setup lang="ts">
import { ReloadOutlined } from "@ant-design/icons-vue";
import { notification } from "ant-design-vue";
import { nextTick, onMounted, ref, type Ref } from "vue";
import Dice from "./Dice.vue";

const numberOne = ref(0);
const numberTwo = ref(0);
const clicks = ref(0);
const clonedAndShuffledArray: Ref<number[]> = ref([]);

function cloneAndShuffleArray(array: number[]) {
  const clonedArray = JSON.parse(JSON.stringify(array));
  for (let i = clonedArray.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [clonedArray[i], clonedArray[j]] = [clonedArray[j], clonedArray[i]];
  }

  clonedAndShuffledArray.value = clonedArray;
}

const originalNumbers = [
  2, 3, 3, 4, 4, 4, 5, 5, 5, 5, 6, 6, 6, 6, 6, 7, 7, 7, 7, 7, 7, 8, 8, 8, 8, 8,
  9, 9, 9, 9, 10, 10, 10, 11, 11, 12,
];

async function handleRoll() {
  if (clonedAndShuffledArray.value.length === 0) {
    cloneAndShuffleArray(originalNumbers);
  }

  numberOne.value = 0;
  numberTwo.value = 0;

  await nextTick(() => {
    clicks.value++;
    const number = clonedAndShuffledArray.value.pop()!;

    localStorage.setItem("lastNumber", JSON.stringify(number));
    setDiceNumber(number);

    localStorage.setItem(
      "shuffledArray",
      JSON.stringify(clonedAndShuffledArray.value)
    );

    localStorage.setItem("clicks", JSON.stringify(clicks.value));
  });
}

function setDiceNumber(number: number) {
  if (number % 2 === 0) {
    numberOne.value = number / 2;
    numberTwo.value = number / 2;
  } else {
    numberOne.value = Math.round(number / 2);
    numberTwo.value = Math.floor(number / 2);
  }
}

function resetDice() {
  cloneAndShuffleArray(originalNumbers);
  clicks.value = 0;
  setDiceNumber(0);
  localStorage.clear();
  openDiceResetNotification();
}

const openDiceResetNotification = () => {
  notification["success"]({
    message: "Dice reset successfuly",
    description: "Dice numbers are reset and reshuffled",
  });
};

onMounted(() => {
  const existingShuffledArray = localStorage.getItem("shuffledArray");
  if (existingShuffledArray) {
    clonedAndShuffledArray.value = JSON.parse(existingShuffledArray);
  }

  const existingClickCounter = localStorage.getItem("clicks");

  if (existingClickCounter) {
    clicks.value = JSON.parse(existingClickCounter);
  }

  const existingLastNumber = localStorage.getItem("lastNumber");

  if (existingLastNumber) {
    setDiceNumber(JSON.parse(existingLastNumber));
  }

  if (clonedAndShuffledArray.value.length === 0) {
    console.log("HITH");
    cloneAndShuffleArray(originalNumbers);
  }
});
</script>

<template>
  <div class="row">
    <Dice :dice-number="numberOne" />
    <Dice :dice-number="numberTwo" />
  </div>
  <div class="row">
    <a-button
      class="button"
      type="primary"
      shape="round"
      size="large"
      @click="handleRoll"
      >Roll
    </a-button>
  </div>
  <div class="footer">
    <div>
      <div class="counters">
        <h4>Numbers left: {{ clonedAndShuffledArray.length }}</h4>
        <h4>Times Rolled: {{ clicks }}</h4>
        <h4>Current Number: {{ numberOne + numberTwo }}</h4>
      </div>
    </div>
    <a-popconfirm
      title="Are you sure you want to reset dice stack？"
      ok-text="Yes"
      cancel-text="No"
      placement="topRight"
      @confirm="resetDice"
    >
      <a-button type="danger" shape="circle" size="large">
        <template #icon>
          <ReloadOutlined />
        </template>
      </a-button>
    </a-popconfirm>
  </div>
</template>

<style scoped>
.row {
  display: flex;
  justify-content: center;
  margin-bottom: 24px;
}

.button {
  background-color: #42b883 !important;
  border-color: #42b883;
  font-size: 18px;
  padding: 0px 40px;
}

.footer {
  display: flex;
  flex: 1;
  justify-content: space-between;
  align-items: flex-end;
}
</style>
