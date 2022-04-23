<script setup lang="ts">
import { ReloadOutlined } from "@ant-design/icons-vue";
import { notification } from "ant-design-vue";
import shuffle from "lodash.shuffle";
import { nextTick, onMounted, ref, watch, type Ref } from "vue";
import Dice from "./Dice.vue";
import EventDice from "./EventDice.vue";
import { EventDiceEnum } from "./types";

const currentPair = ref([0, 0] as [number, number]);
const clicks = ref(0);
const clonedAndShuffledArray: Ref<[number, number][]> = ref([]);
const citiesAndKnights = ref<boolean>(false);
const currentEvent = ref<EventDiceEnum>(EventDiceEnum.Default);

function cloneAndShuffleArray(array: [number, number][]) {
  const clonedArray: [number, number][] = JSON.parse(JSON.stringify(array));
  const shuffledArray = shuffle(clonedArray);

  clonedAndShuffledArray.value = shuffledArray;
}

const numberPairs: [number, number][] = [
  [1, 1],
  [1, 2],
  [2, 1],
  [1, 3],
  [2, 2],
  [3, 1],
  [1, 4],
  [2, 3],
  [3, 2],
  [4, 1],
  [1, 5],
  [2, 4],
  [3, 3],
  [4, 2],
  [5, 1],
  [1, 6],
  [2, 5],
  [3, 4],
  [4, 3],
  [5, 2],
  [6, 1],
  [2, 6],
  [3, 5],
  [4, 4],
  [5, 3],
  [6, 2],
  [3, 6],
  [4, 5],
  [5, 4],
  [6, 3],
  [4, 6],
  [5, 5],
  [6, 4],
  [5, 6],
  [6, 5],
  [6, 6],
];

const events: EventDiceEnum[] = [
  EventDiceEnum.Barbarians,
  EventDiceEnum.Barbarians,
  EventDiceEnum.Barbarians,
  EventDiceEnum.Politics,
  EventDiceEnum.Trade,
  EventDiceEnum.Science,
];

const shuffledAndCloneEvents = ref([] as EventDiceEnum[]);

function shuffleAndCloneEvents(events: EventDiceEnum[]) {
  const cloned: EventDiceEnum[] = JSON.parse(JSON.stringify(events));
  const shuffled = shuffle(cloned);
  shuffledAndCloneEvents.value = shuffled;
}

async function handleRoll() {
  if (clonedAndShuffledArray.value.length === 0) {
    cloneAndShuffleArray(numberPairs);
  }
  if (shuffledAndCloneEvents.value.length === 0) {
    shuffleAndCloneEvents(events);
  }

  currentPair.value = [0, 0];
  currentEvent.value = EventDiceEnum.Default;

  await nextTick(() => {
    clicks.value++;
    const numberPair = clonedAndShuffledArray.value.pop()!;
    const event = shuffledAndCloneEvents.value.pop()!;
    localStorage.setItem("lastNumberPair", JSON.stringify(numberPair));
    localStorage.setItem("lastEvent", JSON.stringify(event));
    currentPair.value = numberPair;
    currentEvent.value = event;
    localStorage.setItem(
      "shuffledArray",
      JSON.stringify(clonedAndShuffledArray.value)
    );
    localStorage.setItem(
      "shuffledEvents",
      JSON.stringify(shuffledAndCloneEvents.value)
    );

    localStorage.setItem("clicks", JSON.stringify(clicks.value));
  });
}

function resetDice() {
  cloneAndShuffleArray(numberPairs);
  shuffleAndCloneEvents(events);
  currentPair.value = [0, 0];
  currentEvent.value = EventDiceEnum.Default;
  clicks.value = 0;
  localStorage.clear();
  openDiceResetNotification();
}

const openDiceResetNotification = () => {
  notification["success"]({
    message: "Dice reset successfuly",
    description: "Dice numbers are reset and reshuffled",
  });
};

watch(
  () => citiesAndKnights.value,
  (newValue) => {
    localStorage.setItem("citiesAndKnights", JSON.stringify(newValue));
  }
);

function checkDiceLocalStorate() {
  const existingShuffledArray = localStorage.getItem("shuffledArray");
  if (existingShuffledArray) {
    clonedAndShuffledArray.value = JSON.parse(existingShuffledArray);
  }

  const existingClickCounter = localStorage.getItem("clicks");

  if (existingClickCounter) {
    clicks.value = JSON.parse(existingClickCounter);
  }

  const existingLastNumber = localStorage.getItem("lastNumberPair");

  if (existingLastNumber) {
    currentPair.value = JSON.parse(existingLastNumber);
  }

  if (clonedAndShuffledArray.value.length === 0) {
    cloneAndShuffleArray(numberPairs);
  }
}

function checkEventDiceLocalStorate() {
  const citiesAndKnightsLocal = localStorage.getItem("citiesAndKnights");
  if (citiesAndKnightsLocal) {
    citiesAndKnights.value = JSON.parse(citiesAndKnightsLocal);
  }

  const existingEventShuffledArray = localStorage.getItem("shuffledEvents");
  if (existingEventShuffledArray) {
    shuffledAndCloneEvents.value = JSON.parse(existingEventShuffledArray);
  }

  const existingLastEvent = localStorage.getItem("lastEvent");

  if (existingLastEvent) {
    currentEvent.value = JSON.parse(existingLastEvent);
  }

  if (shuffledAndCloneEvents.value.length === 0) {
    shuffleAndCloneEvents(events);
  }
}

onMounted(() => {
  checkDiceLocalStorate();
  checkEventDiceLocalStorate();
});
</script>

<template>
  <div class="row">
    <Dice :dice-number="currentPair[0]" :special-dice="citiesAndKnights" />
    <Dice :dice-number="currentPair[1]" />
    <EventDice v-show="citiesAndKnights" :event-to-show="currentEvent" />
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
        <h4>Current Number: {{ currentPair[0] + currentPair[1] }}</h4>
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
  <a-row>
    <div style="margin-right: 16px">Cities and Knights</div>
    <a-switch v-model:checked="citiesAndKnights" />
  </a-row>
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
