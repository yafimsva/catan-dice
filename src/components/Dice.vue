<script setup lang="ts">
import { ref, watch } from "vue";

const props = defineProps<{
  diceNumber: number;
  specialDice?: boolean;
}>();

const numberToShow = ref(0);

watch(
  () => props.diceNumber,
  (newDiceNumber) => {
    if (newDiceNumber === 0) {
      numberToShow.value = newDiceNumber;
      return;
    }

    const randomNumber = Math.floor(Math.random() * (6 - 1) + 1);
    numberToShow.value = randomNumber;
    const interval = setInterval(() => {
      if (numberToShow.value === 6) numberToShow.value = 1;
      numberToShow.value++;
    }, 200);

    setTimeout(() => {
      clearInterval(interval);
      numberToShow.value = newDiceNumber;
    }, 800);
  }
);
</script>

<template>
  <div :class="[specialDice && 'red', 'face']">
    <span class="pip" v-for="number in numberToShow" :key="number" />
  </div>
</template>

<style scoped>
.face {
  display: grid;
  grid-template-areas:
    "a . c"
    "e g f"
    "d . b";

  flex: 0 0 auto;
  margin: 10px;
  padding: 10px;
  width: 104px;
  height: 104px;

  background-color: #e7e7e7;
  box-shadow: inset 0 5px rgb(235, 235, 235), inset 0 -5px #bbb,
    inset 5px 0 #d7d7d7, inset -5px 0 #d7d7d7;
  border-radius: 10%;
}

.red {
  background-color: rgb(184, 3, 3);
  color: white;
  box-shadow: inset 0 5px rgb(183, 3, 3), inset 0 -5px rgb(78, 1, 1),
    inset 5px 0 rgb(155, 0, 0), inset -5px 0 rgb(155, 0, 0);
  border-color: maroon;
}

.pip {
  display: block;
  align-self: center;
  justify-self: center;
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background-color: #333;
  box-shadow: inset 0 3px #111, inset 0 -3px #555;
}

.red .pip {
  background-color: white;
  box-shadow: inset 0 3px rgb(206, 206, 206), inset 0 -3px rgb(238, 238, 238);
}
.pip:nth-child(2) {
  grid-area: b;
}
.pip:nth-child(3) {
  grid-area: c;
}
.pip:nth-child(4) {
  grid-area: d;
}
.pip:nth-child(5) {
  grid-area: e;
}
.pip:nth-child(6) {
  grid-area: f;
}
/* This selects the last pip of odd-valued dice (1, 3, 5) and positions the pip in the center */
.pip:nth-child(odd):last-child {
  grid-area: g;
}
</style>
