<template>
  <q-page class="flex flex-center">
    <Transition
      appear
      enter-active-class="animated fadeIn"
      leave-active-class="animated fadeOut"
      mode="out-in"
    >
      <div
        key="training"
        style="width: 100%; height: 100%"
        v-if="trainingIsActive"
      >
        <!-- <div class="flex flex-center q-pb-md">
          <Transition
            appear
            enter-active-class="animated fadeIn"
            leave-active-class="animated fadeOut"
            mode="out-in"
          >
            <q-icon
              :key="`tu${step}`"
              name="thumb_up"
              color="green"
              size="100px"
            />
            <q-icon
              :key="`td${step}`"
              name="thumb_down"
              color="red"
              size="100px"
            />
          </Transition>
        </div> -->
        <div class="flex flex-center q-pb-md">
          <q-chip color="blue" text-color="white" icon="alarm" size="xl"
            >{{ duration }} сек.</q-chip
          >
          <q-chip icon="event" size="xl"> {{ step }}</q-chip>
        </div>
        <Transition
          appear
          enter-active-class="animated zoomIn"
          leave-active-class="animated zoomOut"
          mode="out-in"
        >
          <div :key="`form${step}`" class="flex flex-center q-pb-md">
            <q-input
              class="input"
              ref="m1"
              v-model="q.m1.val"
              @keyup.enter="nextQuestion"
              type="number"
              outlined
              style="max-width: 200px"
              :readonly="q.m1.known"
              :disable="q.m1.known"
              :autofocus="!q.m1.known"
              @update:model-value="update"
              :key="`m1${step}`"
              :bg-color="q.m1.known ? 'white' : 'orange-2'"
            />
            <q-icon name="fa-solid fa-xmark" size="xl" />
            <!-- <q-icon name="close" size="xl" /> -->
            <q-input
              class="input"
              ref="m2"
              v-model="q.m2.val"
              @keyup.enter="nextQuestion"
              type="number"
              outlined
              style="max-width: 200px"
              :readonly="q.m2.known"
              :disable="q.m2.known"
              :autofocus="!q.m2.known"
              @update:model-value="update('m2')"
              :key="`m2${step}`"
              :bg-color="q.m2.known ? 'white' : 'orange-2'"
            />
            <q-icon name="fa-solid fa-equals" size="xl" />
            <!-- <q-icon name="density_large" size="xl" /> -->
            <q-input
              class="input"
              ref="result"
              v-model="q.result.val"
              @keyup.enter="nextQuestion"
              type="number"
              outlined
              style="max-width: 200px"
              :readonly="q.result.known"
              :disable="q.result.known"
              :autofocus="!q.result.known"
              @update:model-value="update"
              :key="`result${step}`"
              :bg-color="q.result.known ? 'white' : 'orange-2'"
            />
          </div>
        </Transition>
        <div v-if="$q.platform.is.mobile" class="flex flex-center">
          <q-btn color="blue" size="lg" label="Ввод" @click="nextQuestion" />
        </div>
        <div class="flex flex-center">
          <q-btn
            color="red"
            size="lg"
            label="Закончить"
            @click="stopTraining"
          />
        </div>
      </div>
      <div key="initial" v-else>
        <div v-if="step > 1" class="flex flex-center q-pb-md q-pt-md">
          <q-chip icon="event" size="xl"
            >Всего задач решено:
            {{ MultResults.correct + MultResults.incorrect }}</q-chip
          >

          <q-chip
            class="glossy"
            color="green"
            text-color="white"
            icon="star"
            size="xl"
          >
            Правильных ответов: {{ MultResults.correct }}
          </q-chip>

          <q-chip
            class="glossy"
            color="red"
            text-color="white"
            icon="directions"
            size="xl"
          >
            Ошибок: {{ MultResults.incorrect }}
          </q-chip>

          <q-chip color="blue" text-color="white" icon="alarm" size="xl"
            >Время: {{ duration }} сек.</q-chip
          >
        </div>
        <div class="flex flex-center q-pb-md">
          <q-btn
            color="green"
            size="lg"
            label="Начать проверку"
            @click="startTraining"
          />
        </div>
      </div>
    </Transition>
  </q-page>
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

const sleep = (ms) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(true);
    }, ms);
  });
};

const nextQuestion = async () => {
  if (
    q.value.m1.val === null ||
    q.value.m2.val === null ||
    q.value.result.val === null
  )
    return;
  if (isCorrect.value) MultResults.value.correct++;
  else MultResults.value.incorrect++;
  // await sleep(5000);
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

<style lang="scss" scoped>
.input {
  font-size: 1.75rem;
}
</style>
