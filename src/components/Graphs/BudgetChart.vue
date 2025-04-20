<template>
  <div class="chart-container">
    <canvas ref="budgetBarChart"></canvas>
  </div>
</template>

<script>
import {
  Chart as ChartJS,
  BarElement,
  CategoryScale,
  LinearScale,
  Tooltip,
  Legend,
  Title,
  BarController,
} from "chart.js";
import { toRaw } from "vue";

ChartJS.register(
  BarController,
  BarElement,
  CategoryScale,
  LinearScale,
  Tooltip,
  Legend,
  Title
);

export default {
  name: "BudgetBarChart",
  props: {
    total: {
      type: Number,
      required: true,
    },
    categories: {
      type: Object,
      required: true,
      validator: (val) =>
        Object.values(val).every((num) => typeof num === "number"),
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
    categories: "renderChart",
    total: "renderChart",
  },
  methods: {
    renderChart() {
      if (!this.isMounted || !this.$refs.budgetBarChart) return;

      this.destroyChart();

      const rawTotal = toRaw(this.total);
      const categoryEntries = Object.entries(toRaw(this.categories));
      const spentTotal = categoryEntries.reduce((sum, [, val]) => sum + val, 0);
      const remaining = Math.max(rawTotal - spentTotal, 0);

      const labels = [...categoryEntries.map(([key]) => key), "Remaining"];
      const data = [...categoryEntries.map(([, value]) => value), remaining];

      const baseColors = [
        "34, 139, 34",
        "70, 130, 180",
        "255, 140, 0",
        "186, 85, 211",
        "220, 20, 60",
        "0, 191, 255",
        "218, 165, 32",
        "123, 104, 238",
        "60, 179, 113",
        "255, 99, 132",
        "153, 102, 255",
        "255, 159, 64",
        "100, 149, 237",
        "244, 164, 96",
        "0, 206, 209",
      ];

      const backgroundColors = baseColors
        .slice(0, labels.length)
        .map((rgb) => `rgba(${rgb}, 0.2)`);
      const borderColors = baseColors
        .slice(0, labels.length)
        .map((rgb) => `rgba(${rgb}, 1)`);

      this.chartInstance = new ChartJS(this.$refs.budgetBarChart, {
        type: "bar",
        data: {
          labels,
          datasets: [
            {
              data,
              backgroundColor: backgroundColors,
              borderColor: borderColors,
              hoverBackgroundColor: backgroundColors.map((c) =>
                c.replace("0.2", "0.4")
              ),
              borderWidth: 1,
            },
          ],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          scales: {
            y: {
              beginAtZero: true,
              ticks: {
                callback: (value) => `฿${value.toLocaleString()}`,
                color: "#333",
              },
            },
            x: {
              ticks: {
                color: "#333",
              },
            },
          },
          plugins: {
            legend: {
              display: false,
            },
            tooltip: {
              callbacks: {
                label: (tooltipItem) => `฿${tooltipItem.raw.toLocaleString()}`,
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
