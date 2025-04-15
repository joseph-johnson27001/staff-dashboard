<template>
  <div class="chart-container">
    <canvas ref="budgetPieChart"></canvas>
  </div>
</template>

<script>
import {
  Chart as ChartJS,
  ArcElement,
  Tooltip,
  Legend,
  Title,
  PieController, // Change to PieController
} from "chart.js";
import { toRaw } from "vue";

ChartJS.register(PieController, ArcElement, Tooltip, Legend, Title); // Register PieController

export default {
  name: "BudgetPieChart",
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
      if (!this.isMounted || !this.$refs.budgetPieChart) return;

      this.destroyChart();

      const rawTotal = toRaw(this.total);
      const categoryEntries = Object.entries(toRaw(this.categories));
      const spentTotal = categoryEntries.reduce((sum, [, val]) => sum + val, 0);
      const remaining = Math.max(rawTotal - spentTotal, 0);

      const labels = [...categoryEntries.map(([key]) => key), "Remaining"];
      const data = [...categoryEntries.map(([, value]) => value), remaining];
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
      ];

      this.chartInstance = new ChartJS(this.$refs.budgetPieChart, {
        type: "pie", // Change the type to "pie"
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
                  `${tooltipItem.label}: $${tooltipItem.raw.toLocaleString()}`,
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
