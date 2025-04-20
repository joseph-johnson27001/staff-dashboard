<template>
  <div class="chart-container">
    <canvas ref="themesPieChart"></canvas>
  </div>
</template>

<script>
import {
  Chart as ChartJS,
  PieController,
  ArcElement,
  Tooltip,
  Legend,
  Title,
} from "chart.js";
import { toRaw } from "vue";

ChartJS.register(PieController, ArcElement, Tooltip, Legend, Title);

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

      const baseColors = [
        "66, 165, 245",
        "102, 187, 106",
        "255, 167, 38",
        "171, 71, 188",
        "239, 83, 80",
        "38, 198, 218",
        "255, 202, 40",
        "126, 87, 194",
        "38, 166, 154",
        "236, 64, 122",
        "156, 204, 101",
        "92, 107, 192",
        "255, 112, 67",
        "141, 110, 99",
        "41, 182, 246",
        "0, 172, 193",
        "142, 36, 170",
        "253, 216, 53",
        "67, 160, 71",
        "216, 27, 96",
      ];

      const backgroundColors = baseColors
        .slice(0, labels.length)
        .map((rgb) => `rgba(${rgb}, 0.6)`);

      this.chartInstance = new ChartJS(this.$refs.themesPieChart, {
        type: "pie",
        data: {
          labels,
          datasets: [
            {
              data,
              backgroundColor: backgroundColors,

              hoverOffset: 10,
              borderWidth: 1,
              borderColor: "#fff",
            },
          ],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: {
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
}
canvas {
  width: 100% !important;
  height: 100% !important;
}
</style>
