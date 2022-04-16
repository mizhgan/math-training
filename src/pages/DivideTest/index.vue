<template>
  <TransitionGroup name="fade">
    <q-page key="training" v-if="trainingIsActive">
      <div class="flex flex-center q-pb-md">
        <q-chip color="blue" text-color="white" icon="alarm"
          >{{ duration }} сек.</q-chip
        >
        <q-chip icon="event"> {{ step }}</q-chip>
      </div>
      <div class="flex flex-center q-pb-md">
        <q-input
          ref="result"
          v-model="q.result.val"
          v-touch-repeat.enter="nextQuestion"
          type="number"
          filled
          style="max-width: 200px"
          :readonly="q.result.known"
          :autofocus="!q.result.known"
          @update:model-value="update"
          :key="step"
        />
        :
        <q-input
          ref="m1"
          v-model="q.m1.val"
          v-touch-repeat.enter="nextQuestion"
          type="number"
          filled
          style="max-width: 200px"
          :readonly="q.m1.known"
          :autofocus="!q.m1.known"
          @update:model-value="update"
          :key="step"
        />
        =
        <q-input
          ref="m2"
          v-model="q.m2.val"
          v-touch-repeat.enter="nextQuestion"
          type="number"
          filled
          style="max-width: 200px"
          :readonly="q.m2.known"
          :autofocus="!q.m2.known"
          @update:model-value="update('m2')"
          :key="step"
        />
      </div>
      <div v-if="$q.platform.is.mobile" class="flex flex-center">
        <q-btn
          color="blue"
          size="lg"
          label="Ввод"
          @click="nextQuestion"
          :key="step"
        />
      </div>
      <div class="flex flex-center">
        <q-btn
          color="red"
          size="lg"
          label="Закончить"
          @click="stopTraining"
          :key="step"
        />
      </div>
    </q-page>
    <q-page key="initial" v-else class="flex flex-center">
      <div>
        <div v-if="step > 1">
          <div>
            <q-chip icon="event"
              >Всего задач решено:
              {{ MultResults.correct + MultResults.incorrect }}</q-chip
            >
          </div>
          <div>
            <q-chip class="glossy" color="green" text-color="white" icon="star">
              Правильных ответов: {{ MultResults.correct }}
            </q-chip>
          </div>
          <div>
            <q-chip
              class="glossy"
              color="red"
              text-color="white"
              icon="directions"
            >
              Ошибок: {{ MultResults.incorrect }}
            </q-chip>
          </div>
          <div>
            <q-chip color="blue" text-color="white" icon="alarm"
              >Время: {{ duration }} сек.</q-chip
            >
          </div>
        </div>
        <q-btn
          color="green"
          size="lg"
          label="Начать проверку"
          @click="startTraining"
        />
      </div>
    </q-page>
  </TransitionGroup>
</template>

<script setup>
import { ref, computed, inject } from "vue";
// const m1 = ref(null);
// const m2 = ref(null);
// const result = ref(null);
// console.log({ m1, m2, result });

const step = ref(1);
const duration = ref(0);
const timer = ref(null);
const trainingIsActive = ref(false);

const getRandomInt = (max) => {
  return Math.floor(Math.random() * max) + 1;
};
const update = (type) => (val) => {
  console.log({ val });
};
const q = ref({
  m1: {
    val: null,
    known: true,
  },
  m2: {
    val: null,
    known: true,
  },
  result: {
    val: null,
    known: true,
  },
});
const MultResults = ref({
  correct: 0,
  incorrect: 0,
});
const getQuestion = () => {
  const rand1 = getRandomInt(10);
  const rand2 = getRandomInt(10);
  const res = rand1 * rand2;
  const whatToAsk = getRandomInt(3);
  q.value.m1 = {
    val: whatToAsk === 1 ? null : rand1,
    known: whatToAsk === 1 ? false : true,
  };
  q.value.m2 = {
    val: whatToAsk === 2 ? null : rand2,
    known: whatToAsk === 2 ? false : true,
  };
  q.value.result = {
    val: whatToAsk === 3 ? null : res,
    known: whatToAsk === 3 ? false : true,
  };
};
const isCorrect = computed(() => {
  return (
    parseInt(q.value.m1.val) * parseInt(q.value.m2.val) ===
    parseInt(q.value.result.val)
  );
});

const nextQuestion = () => {
  if (isCorrect.value) MultResults.value.correct++;
  else MultResults.value.incorrect++;
  step.value++;
  getQuestion();
};

const startTraining = () => {
  trainingIsActive.value = true;
  step.value = 1;
  duration.value = 0;
  MultResults.value.correct = 0;
  MultResults.value.incorrect = 0;
  getQuestion();
  timer.value = setInterval(() => {
    duration.value++;
  }, 1000);
};
const stopTraining = () => {
  trainingIsActive.value = false;
  clearInterval(timer.value);
};
</script>
