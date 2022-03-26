<script setup lang="ts">
import { ref } from "vue";
import DiceHandler from "./components/DiceHandler.vue";
const numbersLeft = ref(0);
const clickCouter = ref(0);
const currentNumber = ref(0);
</script>

<template>
  <div class="body">
    <div class="header">
      <img
        alt="Vue logo"
        class="logo"
        src="./assets/logo.svg"
        width="50"
        height="50"
      />
      <h2 class="title">Catan Dice Roller</h2>
    </div>

    <DiceHandler
      @numbers-left="numbersLeft = $event"
      @click-couter="clickCouter = $event"
      @current-number="currentNumber = $event"
    />
  </div>
</template>
<style>
@import url("https://fonts.googleapis.com/css2?family=Audiowide&display=swap");

@media screen and (max-width: 500px) {
  .header {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .title {
    margin-top: 16px;
  }
}

.header {
  display: flex;
  align-items: center;
  margin-bottom: 42px;
}

.body {
  font-family: "Audiowide", cursive;
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  padding: 24px;
}

.title {
  margin-left: 24px;
}

.counters {
  margin-left: auto;
}
</style>
