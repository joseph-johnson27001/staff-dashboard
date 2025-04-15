<template>
  <div class="chart-container">
    <canvas ref="ratingsChart"></canvas>
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
  name: "RatingsChart",
  props: {
    labels: Array,
    ratings: Array,
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
    ratings: "renderChart",
  },
  methods: {
    renderChart() {
      if (!this.isMounted || !this.$refs.ratingsChart) return;

      this.destroyChart();

      const rawLabels = toRaw(this.labels) || [];
      const rawRatings = toRaw(this.ratings) || [];

      this.chartInstance = new ChartJS(this.$refs.ratingsChart, {
        type: "line",
        data: {
          labels: rawLabels,
          datasets: [
            {
              label: "Ratings per Month",
              data: rawRatings,
              fill: false,
              borderColor: "#4caf50",
              backgroundColor: "rgba(76, 175, 80, 0.2)",
              tension: 0.2,
              borderWidth: 2,
              pointBackgroundColor: "#4caf50",
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
              min: 0,
              max: 10,
            },
          },
          plugins: {
            legend: { display: false },
            tooltip: {
              callbacks: {
                label: (tooltipItem) =>
                  `Rating: ${tooltipItem.raw.toFixed(1)} / 10`,
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
