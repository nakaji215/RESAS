<template>
  <canvas id="chart" width="400" height="400"></canvas>
</template>

<script setup>
import { ref, watch, onMounted } from "vue";
import axios from "axios";
import Chart from "chart.js/auto";

const props = defineProps({
  selectedPrefectures: {
    type: Array,
    required: true,
  },
});
const emit = defineEmits(["update:selectedPrefectures"]);

const populationData = ref([]);
const chartInstance = ref(null);
const colorMap = {};

let debounceTimeout;

const fetchPopulationData = async () => {
  if (debounceTimeout) {
    clearTimeout(debounceTimeout);
  }

  debounceTimeout = setTimeout(async () => {
    const selectedPrefecturesArray = props.selectedPrefectures;
    populationData.value = []; // リセット

    if (
      !Array.isArray(selectedPrefecturesArray) ||
      selectedPrefecturesArray.length === 0
    ) {
      renderChart();
      return;
    }

    try {
      const data = [];
      for (const prefCode of selectedPrefecturesArray) {
        const response = await axios.get(
          `https://opendata.resas-portal.go.jp/api/v1/population/composition/perYear?cityCode=-&prefCode=${prefCode}`,
          {
            headers: {
              "X-API-KEY": "8QDg39AyxkkLph3QFesrikMB5RULBAmnnuQN7CFR",
            },
          },
        );
        const totalPopulation = response.data.result.data.find(
          (item) => item.label === "総人口",
        );
        if (totalPopulation) {
          const prefName = await getPrefName(prefCode);
          data.push({
            prefCode,
            prefName,
            population: totalPopulation.data,
          });
          if (!colorMap[prefCode]) {
            colorMap[prefCode] = getRandomColor();
          }
        }
      }
      populationData.value = data;
      renderChart();
    } catch (error) {
      console.error("Error fetching population data:", error);
    }
  }, 300); // デバウンスの時間を300msに設定
};

const getPrefName = async (prefCode) => {
  const response = await axios.get(
    "https://opendata.resas-portal.go.jp/api/v1/prefectures",
    {
      headers: { "X-API-KEY": "8QDg39AyxkkLph3QFesrikMB5RULBAmnnuQN7CFR" },
    },
  );
  const prefecture = response.data.result.find(
    (pref) => pref.prefCode === prefCode,
  );
  return prefecture ? prefecture.prefName : "";
};

const renderChart = () => {
  const ctx = document.getElementById("chart").getContext("2d");

  if (chartInstance.value) {
    chartInstance.value.destroy();
  }

  const labels =
    populationData.value.length > 0
      ? populationData.value[0].population.map((item) => item.year)
      : [];

  const datasets = populationData.value.map((prefData) => ({
    label: prefData.prefName,
    data: prefData.population.map((item) => item.value),
    borderColor: colorMap[prefData.prefCode],
    borderWidth: 1,
    fill: false,
  }));

  chartInstance.value = new Chart(ctx, {
    type: "line",
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
      onClick: (event, elements) => {
        if (elements.length > 0) {
          const index = elements[0].datasetIndex;
          const prefCode = populationData.value[index].prefCode;
          emit(
            "update:selectedPrefectures",
            props.selectedPrefectures.filter((code) => code !== prefCode),
          );
        }
      },
    },
  });
};

const getRandomColor = () => {
  const letters = "0123456789ABCDEF";
  let color = "#";
  for (let i = 0; i < 6; i++) {
    color += letters[Math.floor(Math.random() * 16)];
  }
  return color;
};

watch(() => props.selectedPrefectures, fetchPopulationData, { deep: true });

onMounted(fetchPopulationData);
</script>

<style scoped>
canvas {
  display: block;
  margin: 0 auto;
  min-width: 400px;
}

@media screen and (max-width: 768px) {
  canvas {
    width: 100%;
  }
}
</style>
