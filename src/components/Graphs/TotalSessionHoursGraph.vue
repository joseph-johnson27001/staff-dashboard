<template>
  <div class="chart-container">
    <canvas ref="totalSessionHoursChart"></canvas>
  </div>
</template>

<script>
import {
  Chart as ChartJS,
  BarElement,
  CategoryScale,
  LinearScale,
  Title,
  Tooltip,
  Legend,
  BarController,
} from "chart.js";
import { toRaw } from "vue";

ChartJS.register(
  BarController,
  BarElement,
  CategoryScale,
  LinearScale,
  Title,
  Tooltip,
  Legend
);

export default {
  name: "TotalSessionHoursChart",
  props: {
    labels: Array,
    sessionHours: Array,
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
    sessionHours: "renderChart",
  },
  methods: {
    renderChart() {
      if (!this.isMounted || !this.$refs.totalSessionHoursChart) return;

      this.destroyChart();

      const rawLabels = toRaw(this.labels) || [];
      const rawSessionHours = toRaw(this.sessionHours) || [];

      this.chartInstance = new ChartJS(this.$refs.totalSessionHoursChart, {
        type: "bar",
        data: {
          labels: rawLabels,
          datasets: [
            {
              label: "Total Session Hours",
              data: rawSessionHours,
              borderColor: "rgba(34, 139, 34, 1)",
              backgroundColor: "rgba(34, 139, 34, 0.2)",
              hoverBackgroundColor: "rgba(34, 139, 34, 0.4)",
              borderWidth: 1,
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
              ticks: {
                color: "#333",
              },
            },
          },
          plugins: {
            legend: { display: false },
            tooltip: {
              callbacks: {
                label: (tooltipItem) =>
                  `${tooltipItem.raw.toLocaleString()} hours`,
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
}

canvas {
  width: 100% !important;
  height: 100% !important;
}
</style>
