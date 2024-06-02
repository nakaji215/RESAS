<template>
  <div id="app">
    <PrefectureList @update:selectedPrefectures="handleSelectedPrefectures" />
    <LineChart :selectedPrefectures="selectedPrefectures" />
  </div>
</template>

<script setup>
import { ref } from 'vue';
import PrefectureList from './components/PrefectureList.vue';
import LineChart from './components/LineChart.vue';

const selectedPrefectures = ref([]);

const handleSelectedPrefectures = (prefectures) => {
  selectedPrefectures.value = prefectures;
};
</script>

<style scoped>
#app {
  text-align: center;
  background-color: #FFFFFF;
  color: #000000;
}
</style>
