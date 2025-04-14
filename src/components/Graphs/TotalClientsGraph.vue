<template>
  <div class="chart-container">
    <canvas ref="totalClientsChart"></canvas>
  </div>
</template>

<script>
import {
  Chart as ChartJS,
  LineElement,
  PointElement,
  CategoryScale,
  LinearScale,
  Title,
  Tooltip,
  Legend,
  LineController,
} from "chart.js";
import { toRaw } from "vue";

ChartJS.register(
  LineController,
  LineElement,
  PointElement,
  CategoryScale,
  LinearScale,
  Title,
  Tooltip,
  Legend
);

export default {
  name: "TotalClientsChart",
  props: {
    labels: Array,
    counts: Array,
  },
  data() {
    return {
      chartInstance: null,
      isMounted: false,
    };
  },
  mounted() {
    this.isMounted = true;
    this.$nextTick(() => {
      this.renderChart();
      window.addEventListener("resize", this.handleResize);
    });
  },
  beforeUnmount() {
    this.isMounted = false;
    window.removeEventListener("resize", this.handleResize);
    this.destroyChart();
  },
  watch: {
    labels: "renderChart",
    counts: "renderChart",
  },
  methods: {
    renderChart() {
      if (!this.isMounted || !this.$refs.totalClientsChart) return;

      this.destroyChart();

      const rawLabels = toRaw(this.labels) || [];
      const rawCounts = toRaw(this.counts) || [];

      this.chartInstance = new ChartJS(this.$refs.totalClientsChart, {
        type: "line",
        data: {
          labels: rawLabels,
          datasets: [
            {
              label: "Clients per Month",
              data: rawCounts,
              fill: false,
              borderColor: "#0288d1",
              backgroundColor: "rgba(34, 139, 34, 0.2)",
              tension: 0.2,
              borderWidth: 2,
              pointBackgroundColor: "#0288d1",
              pointBorderColor: "#fff",
              pointBorderWidth: 2,
              pointRadius: 5,
            },
          ],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          scales: {
            x: {
              grid: { display: false },
              ticks: { color: "#333" },
            },
            y: {
              grid: { display: true },
              ticks: { color: "#333" },
            },
          },
          plugins: {
            legend: { display: false },
            tooltip: {
              callbacks: {
                label: (tooltipItem) =>
                  `${tooltipItem.raw.toLocaleString()} clients`,
              },
            },
          },
        },
      });
    },

    destroyChart() {
      if (this.chartInstance) {
        toRaw(this.chartInstance).destroy();
        this.chartInstance = null;
      }
    },

    handleResize() {
      if (this.chartInstance) {
        requestAnimationFrame(() => this.chartInstance?.resize());
      }
    },
  },
};
</script>

<style scoped>
.chart-container {
  width: 100%;
  height: 100%;
  min-height: 250px;
}

canvas {
  width: 100% !important;
  height: 100% !important;
}
</style>
