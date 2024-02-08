<script setup lang="ts">
import { computed, ref } from 'vue';

const  sex = ref<'female' | 'male'>('female');
const  height = ref(0);
const  weight = ref(0);
const  age = ref(0);
const  activity = ref(1.55);



const isResultVisible = computed(()=>{
  return height.value > 0 && weight.value > 0 && age.value > 0 
})

const activityOptions = [
  {
    name: 'Малоподвижный образ жизни (тренировок нет или их очень мало)',
    value: 1.2,
  },
  {
    name: 'Небольшая активность (1-3 тренировки в неделю)',
    value: 1.375,
  },
  {
    name: 'Умеренная активность (3-5 тренировок в неделю)',
    value: 1.55,
  },
  {
    name: 'Высокая активность (6-7 тренировок в неделю)',
    value: 1.725,
  },
  {
    name: 'Очень высокая активность (тяжелые тренировки 6-7 дней в неделю)',
    value: 1.9,
  },
]



const result = computed(()=>{
  switch (sex.value) {
    case 'female':
      return  (activity.value * (655.1 + (9.563 * weight.value) + (1.85 * height.value) - (4.676 * age.value))).toFixed(2);
    case 'male':
      return  (activity.value * (88.362 + (13.397 * weight.value) + (4.799 * height.value) - (5.677 * age.value))).toFixed(2);
    default: 
      break;
  }
})

</script>

<template>
  <div>
    <h2 class="text-xl mt-4">Формула Харрисона-Бенедикта</h2>


    <div class="mt-4">Пол</div>

    <label class="flex gap-2">
      <input type="radio" name="sex" value="female" v-model="sex"/>
      Женщина
    </label>
    <label  class="flex gap-2">
      <input type="radio" name="sex"  value="male" v-model="sex"/>
      Мужчина
    </label>

    <label class="mt-4 block">
      Рост (в см):
      <input type="text" v-model="height">
    </label>

    <label class="mt-4 block">
      Возраст (в годах):
      <input type="text" v-model="age">
    </label>

    <label class="mt-4 block">
      Вес (в кг):
      <input type="text" v-model="weight">
    </label>

    <div class="mt-4">Активность</div>

    <label class="flex gap-2" v-for="act in activityOptions">
      <input type="radio" name="activity" :value="act.value" v-model="activity"/>
      {{act.name}}
    </label>


    <div v-if="isResultVisible">
      {{ result }} ккал/сутки
    </div>
  </div>

</template>
<style scoped>
input {
  @apply border px-2 rounded block
}


</style>