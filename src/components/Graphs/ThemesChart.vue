<template>
  <div class="chart-container">
    <canvas ref="themesPieChart"></canvas>
  </div>
</template>

<script>
import {
  Chart as ChartJS,
  ArcElement,
  Tooltip,
  Legend,
  Title,
  DoughnutController, // Use DoughnutController here instead of PieController
} from "chart.js";
import { toRaw } from "vue";

ChartJS.register(DoughnutController, ArcElement, Tooltip, Legend, Title); // Register DoughnutController

export default {
  name: "ThemesChart",
  props: {
    themes: {
      type: Array,
      required: true,
      validator: (val) =>
        val.every(
          (item) =>
            typeof item.label === "string" && typeof item.value === "number"
        ),
    },
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
    themes: "renderChart",
  },
  methods: {
    renderChart() {
      if (!this.isMounted || !this.$refs.themesPieChart) return;

      this.destroyChart();

      const rawThemes = toRaw(this.themes);
      const labels = rawThemes.map((item) => item.label);
      const data = rawThemes.map((item) => item.value);

      const colors = [
        "#42a5f5",
        "#66bb6a",
        "#ffa726",
        "#ab47bc",
        "#ef5350",
        "#26c6da",
        "#ffca28",
        "#7e57c2",
        "#26a69a",
        "#ec407a",
        "#9ccc65",
        "#5c6bc0",
        "#ff7043",
        "#8d6e63",
        "#29b6f6",
        "#00acc1",
        "#8e24aa",
        "#fdd835",
        "#43a047",
        "#d81b60",
      ];

      this.chartInstance = new ChartJS(this.$refs.themesPieChart, {
        type: "doughnut",
        data: {
          labels,
          datasets: [
            {
              data,
              backgroundColor: colors,
              borderColor: "#fff",
              borderWidth: 2,
            },
          ],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: {
              display: true,
              position: "right",
              labels: {
                color: "#333",
              },
            },
            tooltip: {
              callbacks: {
                label: (tooltipItem) =>
                  `${tooltipItem.label}: ${tooltipItem.raw}`,
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
  height: 250px;
  margin-top: 20px;
}
canvas {
  width: 100% !important;
  height: 100% !important;
}
</style>
