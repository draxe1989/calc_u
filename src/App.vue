<script setup lang="ts">
import { computed, ref } from 'vue';

const sex = ref<'female' | 'male'>('female');
const height = ref(165);
const weight = ref(68);
const age = ref(42);
const activity = ref(1.55);



const isResultVisible = computed(() => {
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

const imtOptions = [
  {
    min: 0,
    max: 15.99,
    description: 'Выраженный дефицит массы тела',
    class: 'text-red-500',
    recommendation: 1,
  },
  {
    min: 16,
    max: 18.49,
    description: 'Недостаточная (дефицит) масса тела',
    class: 'text-yellow-500',
    recommendation: 1,
  },
  {
    min: 18.5,
    max: 24.99,
    description: 'Норма',
    class: 'text-green-500',
    recommendation: 0,
  },
  {
    min: 25,
    max: 29.99,
    description: 'Избыточная масса тела (предожирение)',
    class: 'text-yellow-500',
    recommendation: -1,
  },
  {
    min: 30,
    max: 34.99,
    description: 'Ожирение первой степени',
    class: 'text-orange-500',
    recommendation: -1,
  },
  {
    min: 35,
    max: 39.99,
    description: 'Ожирение второй степени',
    class: 'text-red-500',
    recommendation: -1,
  },
  {
    min: 40,
    max: Infinity,
    description: 'Ожирение третьей степени (морбидное)',
    class: 'text-red-500',
    recommendation: -1,
  },
]

const imt = computed(()=>{
  const index =  Math.round( (weight.value * 10000 * 100) / ( height.value * height.value ) )/100

  const description =  imtOptions.find((option)=>{
    return option.min < index && option.max >= index
  }) ||   {
    min: 18.5,
    max: 25,
    description: 'Норма',
    class: 'text-green-800',
    recommendation: 0,
  }

  return {
    index,
    ...description
  }

})


const result = computed<number>(() => {
  switch (sex.value) {
    case 'female':
      return Math.round(activity.value * ((10 * weight.value) + (6.25 * height.value) - (5 * age.value) - 161))
    case 'male':
      return Math.round(activity.value * ((10 * weight.value) + (6.25 * height.value) - (5 * age.value) + 5))
    default:
      return 0
  }
})


const idealWeight = computed(()=>{
  const minRecommendedWeight = (height.value * height.value ) * 18.5 /  10000
  const maxRecommendedWeight = (height.value * height.value ) * 25 /  10000

  let recommendedWeight = 20;

  if (weight.value < minRecommendedWeight) {
    recommendedWeight = minRecommendedWeight
  }
  if (weight.value > maxRecommendedWeight) {
    recommendedWeight = maxRecommendedWeight
  }

  const days = Math.round(Math.abs(weight.value - recommendedWeight) * 7000/500)

  return {
    recommendedWeight,
    days
  }
})




</script>

<template>
  <div>
    <div class="mt-4 flex gap-8">
      <label class="flex gap-2">
        <input type="radio" name="sex" value="female" v-model="sex" />
        Женщина
      </label>
      <label class="flex gap-2">
        <input type="radio" name="sex" value="male" v-model="sex" />
        Мужчина
      </label>
    </div>

    <label class="mt-4 flex gap-4">
      <input type="number" v-model="height">
      <div class="grow">
        Рост (в см):
      </div>
    </label>

    <label class="mt-4 flex gap-4">
      <input type="number" v-model="age">
      <div class="grow">
        Возраст (в годах):
      </div>
    </label>
    <label class="mt-4 flex gap-4">
      <input type="number" v-model="weight">
      <div class="grow">
        Вес (в кг):
      </div>
    </label>

    <div class="mt-4 ">Активность</div>

    <label class="flex gap-2" v-for="act in activityOptions">
      <input type="radio" name="activity" :value="act.value" v-model="activity" />
      {{ act.name }}
    </label>

    <h3 class="mt-4 text-center" :class="imt.class">Индекс массы тела: <b>{{imt.index.toFixed(2)}}</b></h3>
    <p class="text-center" :class="imt.class">{{imt.description}}</p>

    <template v-if="imt.recommendation !==0">
      <h4 class="mt-4 text-center">Рекомендованный вес <b>{{ ( idealWeight.recommendedWeight ).toFixed() }}</b> кг</h4>
      <h4 class="text-center">Дней диеты: <b>{{ ( idealWeight.days ).toFixed() }}</b></h4>
    </template>


    <template v-if="isResultVisible">
      <h4 class="mt-4 text-center">Для снижения веса</h4>
      <div class="text-lg text-center rounded" :class="{'bg-green-200': imt.recommendation < 0}">
        <b>{{ (result - 500).toLocaleString() }}</b> ккал/сутки
      </div>
      <h4 class="mt-4 text-center">Для поддержания веса</h4>
      <div class="text-lg text-center rounded" :class="{'bg-green-200': imt.recommendation === 0}">
        <b>{{ (result).toLocaleString() }}</b> ккал/сутки
      </div>
      <h4 class="mt-4 text-center" :class="{'bg-green-200': imt.recommendation > 0}">Для увеличения веса</h4>
      <div class="text-lg text-center rounded">
        <b>{{ (result + 500).toLocaleString() }}</b> ккал/сутки
      </div>
    </template>

  </div>
</template>
<style scoped>
input[type="number"] {
  @apply border px-2 rounded block w-24
}
</style>