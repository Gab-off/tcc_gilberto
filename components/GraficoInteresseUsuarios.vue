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

// Dados do gráfico
const LABELS = [
  'Debater temas técnicos', 
  'Compartilhar projetos / receber feedback', 
  'Acompanhar tópicos e aprender', 
  'Fazer perguntas e obter respostas rápidas'
];

const DADOS = [12.5, 15.6, 21.9, 50];
const CORES_PADRAO = ['#36A2EB', '#FF6384', '#FFCE56', '#AAAAAA'];
const COR_DESTAQUE = '#28a745'; // verde para a maior fatia
const COR_INATIVA = '#555555';

// Controle de cliques
const relativeClicks = computed(() => (props.clicks >= props.startAt ? props.clicks - props.startAt : -1));

// Dados do gráfico
const chartData = computed(() => {
  if (relativeClicks.value < 1) {
    return { labels: LABELS, datasets: [{ data: DADOS, backgroundColor: CORES_PADRAO }] };
  } else {
    // Após clique: destacar a maior fatia (50%) em verde
    const background = DADOS.map((v, i) => (i === 3 ? COR_DESTAQUE : COR_INATIVA));
    return { labels: LABELS, datasets: [{ data: DADOS, backgroundColor: background }] };
  }
});

// Opções do gráfico
const chartOptions = computed(() => ({
  responsive: true,
  maintainAspectRatio: false,
  animation: { duration: 800, easing: 'easeInOutCubic' },
  devicePixelRatio: window.devicePixelRatio,
  plugins: {
    title: {
      display: true,
      text: 'Tipos de interação desejadas em fóruns',
      font: { size: 20 },
      color: '#FFFFFF'
    },
    legend: {
      position: 'top',
      labels: { color: '#E0E0E0', font: { size: 14 } }
    },
    tooltip: {
      callbacks: { label: ctx => `${ctx.label}: ${ctx.parsed}%` }
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
      50% querem fazer perguntas e obter respostas rápidas — a maioria
    </p>
  </div>
</template>
