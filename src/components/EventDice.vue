<script setup lang="ts">
import { computed, ref, watch } from "vue";
import DefaultSvg from "../assets/default.svg";
import BarbariansSvg from "../assets/dice_event_barbarian.svg";
import PoliticsSvg from "../assets/dice_event_politics.svg";
import ScienceSvg from "../assets/dice_event_science.svg";
import TradeSvg from "../assets/dice_event_trade.svg";
import { EventDiceEnum } from "./types";

const props = defineProps<{
  eventToShow: EventDiceEnum;
}>();

const finalImage = ref<EventDiceEnum>(EventDiceEnum.Default);

const imageToShow = computed(() => {
  switch (finalImage.value) {
    case EventDiceEnum.Barbarians:
      return BarbariansSvg;
    case EventDiceEnum.Politics:
      return PoliticsSvg;
    case EventDiceEnum.Trade:
      return TradeSvg;
    case EventDiceEnum.Science:
      return ScienceSvg;
    default:
      return DefaultSvg;
  }
});

watch(
  () => props.eventToShow,
  (newEvent) => {
    if (newEvent === EventDiceEnum.Default) {
      finalImage.value = newEvent;
      return;
    }

    const randomNumber = Math.floor(Math.random() * (3 - 1) + 0);
    finalImage.value = randomNumber;
    const interval = setInterval(() => {
      if (finalImage.value === 3) finalImage.value = 0;
      finalImage.value++;
    }, 200);

    setTimeout(() => {
      clearInterval(interval);
      finalImage.value = newEvent;
    }, 800);
  }
);
</script>

<template>
  <img :src="imageToShow" class="face" />
</template>

<style scoped>
.face {
  margin: 10px;
  width: 104px;
  height: 104px;
}
</style>
