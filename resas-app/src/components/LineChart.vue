<template>
  <canvas id="chart" width="400" height="400"></canvas>
</template>

<!-- <script setup>
import { ref, watch, onMounted } from 'vue';
import axios from 'axios';
import Chart from 'chart.js/auto';

const props = defineProps({
  selectedPrefectures: {
    type: Object,
    required: true,
  },
});

const populationData = ref([]);
const chartInstance = ref(null);
const colorMap = {};

const fetchPopulationData = async () => {
  const selectedPrefecturesArray = props.selectedPrefectures.prefectures;
  if (!Array.isArray(selectedPrefecturesArray)) {
    return;
  }
  
  try {
    const data = [];
    for (const prefCode of selectedPrefecturesArray) {
      const response = await axios.get(
        `https://opendata.resas-portal.go.jp/api/v1/population/composition/perYear?cityCode=-&prefCode=${prefCode}`, 
        { headers: { 'X-API-KEY': '8QDg39AyxkkLph3QFesrikMB5RULBAmnnuQN7CFR' } }
      );
      const totalPopulation = response.data.result.data.find(item => item.label === '総人口');
      if (totalPopulation) {
        const selectedPrefecture = prefectures.value.find(pref => pref.prefCode === prefCode);
        const prefName = selectedPrefecture ? selectedPrefecture.prefName : '';
        data.push({
          prefCode,
          prefName,
          population: totalPopulation.data
        });
        // 色が割り当てられていない場合は、新しい色を割り当てる
        if (!colorMap[prefCode]) {
          colorMap[prefCode] = getRandomColor();
        }
      }
    }
    populationData.value = data;
    renderChart();
  } catch (error) {
    console.error('Error fetching population data:', error);
  }
};



const renderChart = () => {
  const ctx = document.getElementById('chart').getContext('2d');

  if (chartInstance.value) {
    chartInstance.value.destroy();
  }

  const labels = populationData.value.length > 0 ? populationData.value[0].population.map(item => item.year) : [];

  const datasets = populationData.value.map(prefData => ({
    label: prefData.prefName, // ここで prefData.prefName を使用
    data: prefData.population.map(item => item.value),
    borderColor: colorMap[prefData.prefCode], 
    borderWidth: 1,
    fill: false,
  }));



  chartInstance.value = new Chart(ctx, {
    type: 'line',
    data: {
      labels,
      datasets,
    },
    options: {
      scales: {
        y: {
          beginAtZero: true,
        },
      },
    },
  });
};

const getRandomColor = () => {
  const letters = '0123456789ABCDEF';
  let color = '#';
  for (let i = 0; i < 6; i++) {
    color += letters[Math.floor(Math.random() * 16)];
  }
  return color;
};

// 監視しているプロパティの変更があった場合にデータをフェッチしてグラフを更新する
watch(() => props.selectedPrefectures, fetchPopulationData, { deep: true });

onMounted(fetchPopulationData);
</script> -->

<script setup>
import { ref, watch, onMounted } from 'vue';
import axios from 'axios';
import Chart from 'chart.js/auto';

const props = defineProps({
  selectedPrefectures: {
    type: Object,
    required: true,
  },
});

const populationData = ref([]);
const chartInstance = ref(null);
const colorMap = {};

const fetchPopulationData = async () => {
  const selectedPrefecturesArray = props.selectedPrefectures.prefectures;
  if (!Array.isArray(selectedPrefecturesArray)) {
    return;
  }
  
  try {
    const data = [];
    for (const prefCode of selectedPrefecturesArray) {
      const response = await axios.get(
        `https://opendata.resas-portal.go.jp/api/v1/population/composition/perYear?cityCode=-&prefCode=${prefCode}`, 
        { headers: { 'X-API-KEY': '8QDg39AyxkkLph3QFesrikMB5RULBAmnnuQN7CFR' } }
      );
      const totalPopulation = response.data.result.data.find(item => item.label === '総人口');
      if (totalPopulation) {
        data.push({
          prefCode,
          population: totalPopulation.data
        });
        // 色が割り当てられていない場合は、新しい色を割り当てる
        if (!colorMap[prefCode]) {
          colorMap[prefCode] = getRandomColor();
        }
      }
    }
    populationData.value = data;
    renderChart();
  } catch (error) {
    console.error('Error fetching population data:', error);
  }
};

const renderChart = () => {
  const ctx = document.getElementById('chart').getContext('2d');

  if (chartInstance.value) {
    chartInstance.value.destroy();
  }

  const labels = populationData.value.length > 0 ? populationData.value[0].population.map(item => item.year) : [];

  const datasets = populationData.value.map(prefData => ({
    label: prefData.prefName,
    data: prefData.population.map(item => item.value),
    borderColor: colorMap[prefData.prefCode], 
    borderWidth: 1,
    fill: false,
  }));


  chartInstance.value = new Chart(ctx, {
    type: 'line',
    data: {
      labels,
      datasets,
    },
    options: {
      scales: {
        y: {
          beginAtZero: true,
        },
      },
    },
  });
};

const getRandomColor = () => {
  const letters = '0123456789ABCDEF';
  let color = '#';
  for (let i = 0; i < 6; i++) {
    color += letters[Math.floor(Math.random() * 16)];
  }
  return color;
};

// 監視しているプロパティの変更があった場合にデータをフェッチしてグラフを更新する
watch(() => props.selectedPrefectures, fetchPopulationData, { deep: true });

onMounted(fetchPopulationData);
</script>

<style scoped>
canvas {
  display: block;
  margin: 0 auto;
}
</style>


<style scoped>
canvas {
  display: block;
  margin: 0 auto;
}
</style>
