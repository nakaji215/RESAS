<template>
  <div id="app">
    <h1>RESAS API</h1>
    <PrefectureList
      :selectedPrefectures="selectedPrefectures"
      @update:selectedPrefectures="handleSelectedPrefectures"
    />
    <LineChart
      :selectedPrefectures="selectedPrefectures"
      @update:selectedPrefectures="handleSelectedPrefectures"
    />
  </div>
</template>

<script setup>
import { ref } from "vue";
import PrefectureList from "./components/PrefectureList.vue";
import LineChart from "./components/LineChart.vue";

const selectedPrefectures = ref([]);

const handleSelectedPrefectures = (prefectures) => {
  selectedPrefectures.value = prefectures;
};
</script>

<style scoped>
#app {
  text-align: center;
  background-color: #ffffff;
  color: #000000;
}
</style>
