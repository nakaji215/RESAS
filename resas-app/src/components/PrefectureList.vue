<template>
  <div v-if="prefectures">
    <ul class="prefecture-list">
      <li v-for="prefecture in prefectures" :key="prefecture.prefCode">
        <input 
          type="checkbox" 
          :id="prefecture.prefCode" 
          @change="toggleSelection(prefecture.prefCode)" 
        />
        <label :for="prefecture.prefCode">{{ prefecture.prefName }}</label>
      </li>
    </ul>
  </div>
  <div v-else>Loading...</div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

// emit関数を定義する
const emit = defineEmits(['update:selectedPrefectures']);

const prefectures = ref(null);
const selectedPrefectures = ref([]);

onMounted(() => {
  const prefectures_url = 'https://opendata.resas-portal.go.jp/api/v1/prefectures';
  axios
    .get(prefectures_url, { headers: { 'X-API-KEY': '8QDg39AyxkkLph3QFesrikMB5RULBAmnnuQN7CFR' } })
    .then(response => {
      prefectures.value = response.data.result;
    })
    .catch(error => {
      console.error('Error fetching prefectures:', error);
    });
});

const toggleSelection = (prefCode) => {
  const selectedPrefecture = prefectures.value.find(pref => pref.prefCode === prefCode);
  if (selectedPrefecture) {
    const prefName = selectedPrefecture.prefName;
    const index = selectedPrefectures.value.indexOf(prefCode);
    if (index > -1) {
      selectedPrefectures.value.splice(index, 1);
    } else {
      selectedPrefectures.value.push(prefCode);
    }
    emitSelectedPrefectures(prefName);
  }
};

const emitSelectedPrefectures = (prefName) => {
  emit('update:selectedPrefectures', { prefectures: selectedPrefectures.value, selectedPrefectureName: prefName });
};

</script>

<style scoped>
.prefecture-list {
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
