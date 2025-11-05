<script setup>
import { Pie } from 'vue-chartjs';
import { Chart as ChartJS, Title, Tooltip, Legend, ArcElement, CategoryScale } from 'chart.js';
import { computed } from 'vue';

ChartJS.register(Title, Tooltip, Legend, ArcElement, CategoryScale);

// Props do Slidev
const props = defineProps({
  clicks: { type: Number, default: 0 },
  startAt: { type: Number, default: 0 }
});

// Dados originais
const LABELS_ORIGINAIS = ['Sim, com frequência', 'Às vezes', 'Raramente', 'Nunca'];
const DADOS_ORIGINAIS = [46.9, 46.9, 3.1, 3.1];
const CORES_ORIGINAIS = ['#36A2EB', '#FF6384', '#FFCE56', '#4BC0C0'];
const COR_DESTACADA_PRINCIPAL = '#28a745';
const COR_INATIVA = '#555555';

// Controle de cliques
const relativeClicks = computed(() => (props.clicks >= props.startAt ? props.clicks - props.startAt : -1));

// Dados do gráfico
const chartData = computed(() => {
  if (relativeClicks.value < 1) {
    return {
      labels: LABELS_ORIGINAIS,
      datasets: [{ data: DADOS_ORIGINAIS, backgroundColor: CORES_ORIGINAIS }]
    };
  } else {
    const somaDificuldade = DADOS_ORIGINAIS[0] + DADOS_ORIGINAIS[1];
    const outros = DADOS_ORIGINAIS[2] + DADOS_ORIGINAIS[3];
    return {
      labels: ['Já teve dificuldade', 'Não teve ou raramente teve'],
      datasets: [{ data: [somaDificuldade, outros], backgroundColor: [COR_DESTACADA_PRINCIPAL, COR_INATIVA] }]
    };
  }
});

// Opções do gráfico
const chartOptions = computed(() => ({
  responsive: true,
  maintainAspectRatio: false,
  animation: { duration: 800, easing: 'easeInOutCubic' },
  devicePixelRatio: window.devicePixelRatio, // 🔹 aumenta nitidez
  plugins: {
    title: {
      display: true,
      text: 'Você já teve dificuldade em encontrar ajuda para dúvidas durante os estudos?',
      font: { size: 20 },
      color: '#FFFFFF'
    },
    legend: {
      position: 'top',
      labels: { color: '#E0E0E0', font: { size: 14 } }
    },
    tooltip: {
      callbacks: {
        label: ctx => `${ctx.label}: ${ctx.parsed}%`
      }
    }
  }
}));

const isVisible = computed(() => relativeClicks.value >= 0);
const showFinalText = computed(() => relativeClicks.value >= 1);
</script>

<template>
  <div
    class="w-full h-full flex flex-col justify-center items-center p-4 transition-opacity duration-700"
    :class="isVisible ? 'opacity-100' : 'opacity-0'"
  >
    <div class="flex-grow flex justify-center items-center w-full h-full">
      <Pie :data="chartData" :options="chartOptions" class="w-full h-full"/>
    </div>

    <p
     class="text-md font-semibold text-green-500 mt-4 text-center transition-opacity duration-700 transform transition-transform"
      :class="[showFinalText ? 'opacity-100 translate-y-0' : 'opacity-0 translate-y-4']"
    >
      {{ (DADOS_ORIGINAIS[0] + DADOS_ORIGINAIS[1]).toFixed(1) }}% já teve dificuldade em encontrar ajuda!
    </p>
  </div>
</template>
