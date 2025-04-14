<template>
  <div class="chart-container">
    <canvas ref="totalClientsChart"></canvas>
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
  watch: {
    labels: "renderChart",
    counts: "renderChart",
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
  methods: {
    renderChart() {
      if (!this.isMounted || !this.$refs.totalClientsChart) return;

      this.destroyChart();

      this.chartInstance = new ChartJS(this.$refs.totalClientsChart, {
        type: "bar",
        data: {
          labels: this.labels || [],
          datasets: [
            {
              label: "Clients per Month",
              data: this.counts || [],
              backgroundColor: "rgba(0, 123, 255, 0.2)",
              hoverBackgroundColor: "rgba(0, 123, 255, 0.4)",
              borderColor: "rgba(0, 123, 255, 1)",
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
                stepSize: 5,
                color: "#333",
              },
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
        this.chartInstance.destroy();
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
