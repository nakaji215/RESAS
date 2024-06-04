<template>
  <div v-if="prefectures">
    <ul class="prefecture-list">
      <li v-for="prefecture in prefectures" :key="prefecture.prefCode">
        <input
          type="checkbox"
          :id="prefecture.prefCode"
          :checked="selectedPrefectures.includes(prefecture.prefCode)"
          @change="toggleSelection(prefecture.prefCode)"
        />
        <label :for="prefecture.prefCode">{{ prefecture.prefName }}</label>
      </li>
    </ul>
  </div>
  <div v-else>Loading...</div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";

const emit = defineEmits(["update:selectedPrefectures"]);
const props = defineProps({
  selectedPrefectures: {
    type: Array,
    required: true,
  },
});

const prefectures = ref(null);

onMounted(() => {
  const prefectures_url =
    "https://opendata.resas-portal.go.jp/api/v1/prefectures";
  axios
    .get(prefectures_url, {
      headers: { "X-API-KEY": "8QDg39AyxkkLph3QFesrikMB5RULBAmnnuQN7CFR" },
    })
    .then((response) => {
      prefectures.value = response.data.result;
    })
    .catch((error) => {
      console.error("Error fetching prefectures:", error);
    });
});

const toggleSelection = (prefCode) => {
  const index = props.selectedPrefectures.indexOf(prefCode);
  if (index > -1) {
    emit(
      "update:selectedPrefectures",
      props.selectedPrefectures.filter((code) => code !== prefCode),
    );
  } else {
    emit("update:selectedPrefectures", [
      ...props.selectedPrefectures,
      prefCode,
    ]);
  }
};
</script>

<style scoped>
.prefecture-list {
  max-width: 960px;
  margin: 0 auto 20px;
  display: flex;
  flex-wrap: wrap;
  grid-gap: 24px;
}
li {
  list-style-type: none;
}

label {
  cursor: pointer;
}
</style>
