<template>
  <div id="app">
    <TopNav />
    <div class="content-wrapper">
      <div class="main-content">
        <h3>Overall Information</h3>
        <!-- stat Cards Row -->
        <div class="stats-grid">
          <StatisticsCard
            v-for="stat in stats"
            :key="stat.statName"
            :icon-class="stat.iconClass"
            :stat-name="stat.statName"
            :value="stat.value"
            :icon-color="stat.iconColor"
            :icon-container-color="stat.iconContainerColor"
          />
        </div>

        <div class="graph-container">
          <GraphCard title="Themes">
            <ThemesChart :themes="themes" />
          </GraphCard>
          <GraphCard title="Expenses">
            <BudgetChart
              :total="budget.total"
              :categories="budget.categories"
            />
          </GraphCard>
        </div>

        <div class="counselling-heading">
          <h3>Counselling Information</h3>
          <div class="dropdowns">
            <!-- Dropdown for Weekly/Monthly Selection -->
            <div class="dropdown-container">
              <label for="view-select"></label>
              <select id="view-select" v-model="viewType">
                <option value="monthly">Monthly</option>
                <option value="weekly">Weekly</option>
              </select>
            </div>

            <!-- Dropdown for Counsellor Selection -->
            <div class="dropdown-container">
              <label for="counsellor-select"></label>
              <select id="counsellor-select" v-model="selectedCounsellor">
                <option
                  v-for="(data, name) in counsellorData"
                  :key="name"
                  :value="name"
                >
                  {{ name }}
                </option>
              </select>
            </div>
          </div>
        </div>

        <!-- Graph -->
        <div class="graph-container">
          <GraphCard
            :title="`Total Clients – ${selectedCounsellor} (${
              viewType.charAt(0).toUpperCase() + viewType.slice(1)
            })`"
          >
            <TotalClientsChart
              :labels="
                viewType === 'monthly'
                  ? clientChartLabels
                  : clientChartWeeklyLabels
              "
              :counts="
                viewType === 'monthly'
                  ? totalClients[selectedCounsellor]
                  : totalClientsWeekly[selectedCounsellor]
              "
            />
          </GraphCard>

          <GraphCard
            :title="`Total Session Hours – ${selectedCounsellor} (${
              viewType.charAt(0).toUpperCase() + viewType.slice(1)
            })`"
          >
            <TotalSessionHoursChart
              :labels="
                viewType === 'monthly'
                  ? clientChartLabels
                  : clientChartWeeklyLabels
              "
              :session-hours="
                viewType === 'monthly'
                  ? sessionHours[selectedCounsellor]
                  : sessionHoursWeekly[selectedCounsellor]
              "
            />
          </GraphCard>

          <GraphCard
            :title="`Counselling Feedback – ${selectedCounsellor} (${
              viewType.charAt(0).toUpperCase() + viewType.slice(1)
            })`"
          >
            <RatingsGraph
              :labels="
                viewType === 'monthly'
                  ? clientChartLabels
                  : clientChartWeeklyLabels
              "
              :ratings="
                viewType === 'monthly'
                  ? ratingMonthlyScores[selectedCounsellor]
                  : ratingWeeklyScores[selectedCounsellor]
              "
            />
          </GraphCard>

          <CommentsTable
            :comments="comments"
            :view-type="viewType"
            :selected-counsellor="selectedCounsellor"
          />
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import TopNav from "./components/Navigation/TopNav.vue";
import StatisticsCard from "./components/UI/StatisticsCard.vue";
import GraphCard from "./components/UI/GraphCard.vue";
import BudgetChart from "./components/Graphs/BudgetChart.vue";
import ThemesChart from "./components/Graphs/ThemesChart.vue";
import TotalClientsChart from "./components/Graphs/TotalClientsGraph.vue";
import TotalSessionHoursChart from "./components/Graphs/TotalSessionHoursGraph.vue";
import RatingsGraph from "./components/Graphs/RatingsGraph.vue";
import CommentsTable from "@/components/Tables/CommentsTable.vue";

export default {
  name: "App",
  components: {
    TopNav,
    StatisticsCard,
    GraphCard,
    BudgetChart,
    ThemesChart,
    TotalClientsChart,
    TotalSessionHoursChart,
    RatingsGraph,
    CommentsTable,
  },
  data() {
    return {
      comments: [
        {
          text: "Joanne always listens attentively and offers thoughtful insights.",
          counsellor: "Joanne Barnuevo",
          date: "2025-04-05",
        },
        {
          text: "Her calm and compassionate nature really put me at ease.",
          counsellor: "Joanne Barnuevo",
          date: "2025-04-08",
        },
        {
          text: "Rebecca made me feel heard and supported throughout.",
          counsellor: "Rebecca McDonnell",
          date: "2025-04-06",
        },
        {
          text: "She is incredibly understanding and gives great advice.",
          counsellor: "Rebecca McDonnell",
          date: "2025-04-09",
        },
        {
          text: "Elly has a warm and reassuring presence that helped me open up.",
          counsellor: "Elly Messo",
          date: "2025-04-04",
        },
        {
          text: "I felt truly respected and supported during my session with Elly.",
          counsellor: "Elly Messo",
          date: "2025-04-10",
        },
        {
          text: "Lorena was professional and kind — I felt very comfortable talking to her.",
          counsellor: "Lorena Halpin-Doyle",
          date: "2025-04-03",
        },
        {
          text: "She helped me see things from a new, more positive perspective.",
          counsellor: "Lorena Halpin-Doyle",
          date: "2025-04-07",
        },
      ],
      themes: [
        { label: "Anxiety", value: 20 },
        { label: "Disordered Eating", value: 2 },
        { label: "Neurodiversity", value: 18 },
        { label: "Suspected Neurodiversity", value: 6 },
        { label: "Academic Support", value: 12 },
        { label: "Interpersonal Dynamics", value: 6 },
        { label: "Intrapersonal", value: 6 },
        { label: "Grief & Loss", value: 7 },
        { label: "Trauma", value: 15 },
        { label: "Emotional Regulation", value: 10 },
        { label: "Low Mood", value: 8 },
        { label: "Risk Taking Behaviours", value: 12 },
        { label: "Sleep", value: 18 },
        { label: "School Transitions", value: 9 },
        { label: "School", value: 5 },
      ],
      budget: {
        total: 50000,
        categories: {
          "Furniture & Equipment": 12000,
          "Student Materials": 1600,
          "Training & Development": 60000,
          Miscellaneous: 3000,
          Remaining: 3400,
        },
      },
      selectedCounsellor: "All Counsellors",
      counsellorData: {
        "All Counsellors": "All Counsellors",
        "Rebecca McDonnell": "Rebecca McDonnell",
        "Elly Messo": "Elly Messo",
        "Lorena Halpin-Doyle": "Lorena Halpin-Doyle",
        "Joanne Barnuevo": "Joanne Barnuevo",
      },
      stats: [
        {
          iconClass: "fas fa-user-friends",
          statName: "Students In Counselling",
          value: "75",
          iconColor: "#28c76f",
          iconContainerColor: "rgba(40, 199, 111, 0.1)",
        },
        {
          iconClass: "fas fa-ban",
          statName: "No Shows",
          value: "5%",
          iconColor: "#ff6f91",
          iconContainerColor: "rgba(255, 111, 145, 0.1)",
        },
        {
          iconClass: "fas fa-clock",
          statName: "Waitlist",
          value: "3",
          iconColor: "#ea5455",
          iconContainerColor: "rgba(234, 84, 85, 0.1)",
        },
        {
          iconClass: "fas fa-chair",
          statName: "Available Slots",
          value: "9",
          iconColor: "#00cfe8",
          iconContainerColor: "rgba(0, 207, 232, 0.1)",
        },
        {
          iconClass: "fas fa-user-clock",
          statName: "Average Wait Time",
          value: "6 days",
          iconColor: "#7367f0",
          iconContainerColor: "rgba(115, 103, 240, 0.1)",
        },
        {
          iconClass: "fas fa-hand-paper",
          statName: "Total Students Who Accessed Counselling",
          value: "130",
          iconColor: "#ff9f43",
          iconContainerColor: "rgba(255, 159, 67, 0.1)",
        },
        {
          iconClass: "fas fa-wallet",
          statName: "Total Budget",
          value: "฿80,000",
          iconColor: "#34c38f",
          iconContainerColor: "rgba(52, 195, 143, 0.1)",
        },
        {
          iconClass: "fas fa-piggy-bank",
          statName: "Budget Remaining",
          value: "฿25,000",
          iconColor: "#ffbc00",
          iconContainerColor: "rgba(255, 188, 0, 0.1)",
        },
      ],
      clientChartLabels: [
        "Aug",
        "Sep",
        "Oct",
        "Nov",
        "Dec",
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "Jun",
      ],
      clientChartWeeklyLabels: [
        " 1",
        " 2",
        " 3",
        " 4",
        " 5",
        " 6",
        " 7",
        " 8",
        " 9",
        " 10",
        " 11",
        " 12",
        " 13",
        " 14",
        " 15",
        " 16",
        " 17",
        " 18",
        " 19",
        " 20",
        " 21",
        " 22",
        " 23",
        " 24",
        " 25",
        " 26",
        " 27",
        " 28",
        " 29",
        " 30",
        " 31",
        " 32",
        " 33",
        " 34",
        " 35",
        " 36",
        " 37",
        " 38",
        " 39",
        " 40",
        " 41",
        " 42",
        " 43",
        " 44",
        " 45",
        " 46",
        " 47",
        " 48",
        " 49",
        " 50",
        " 51",
        " 52",
      ],
      totalClients: {
        "Rebecca McDonnell": [18, 16, 15, 18, 16, 15, 17, 18, 16, 15, 17, 16],
        "Elly Messo": [14, 15, 16, 14, 15, 16, 14, 15, 14, 16, 15, 14],
        "Lorena Halpin-Doyle": [13, 14, 15, 13, 14, 15, 13, 14, 15, 13, 14, 15],
        "Joanne Barnuevo": [11, 12, 10, 11, 12, 11, 12, 10, 11, 12, 11, 12],
        "All Counsellors": [55, 57, 56, 56, 57, 57, 56, 57, 56, 56, 57, 57],
      },
      totalClientsWeekly: {
        "Rebecca McDonnell": [
          15, 18, 14, 17, 16, 18, 15, 17, 16, 18, 15, 16, 17, 18, 16, 15, 17,
          18, 16, 15, 16, 17, 15, 16, 18, 17, 16, 15, 17, 16, 18, 15, 17, 16,
          15, 16, 17, 18, 15, 16, 17, 16, 18, 15, 16, 17, 16, 17, 16, 15, 18,
          17, 16, 15,
        ],
        "Elly Messo": [
          12, 14, 11, 13, 14, 12, 13, 14, 12, 13, 14, 12, 14, 13, 12, 11, 13,
          14, 12, 13, 14, 12, 13, 14, 12, 13, 14, 12, 11, 13, 14, 12, 11, 12,
          13, 14, 12, 13, 14, 12, 11, 13, 14, 12, 13, 12, 14, 13, 14, 13, 12,
          11,
        ],
        "Lorena Halpin-Doyle": [
          11, 13, 12, 14, 13, 14, 12, 13, 14, 13, 12, 13, 14, 13, 12, 13, 12,
          13, 14, 13, 12, 13, 14, 12, 13, 14, 13, 12, 13, 14, 13, 12, 13, 12,
          13, 14, 13, 12, 13, 14, 13, 12, 13, 12, 13, 14, 13, 12, 13, 14, 13,
          12,
        ],
        "Joanne Barnuevo": [
          10, 12, 11, 13, 12, 14, 12, 13, 12, 11, 10, 12, 11, 12, 11, 10, 11,
          12, 11, 12, 13, 12, 11, 12, 11, 12, 11, 10, 12, 11, 12, 10, 11, 12,
          11, 12, 10, 11, 12, 11, 12, 10, 11, 12, 11, 12, 11, 12, 11, 10, 11,
          12, 11,
        ],
        "All Counsellors": [
          48, 57, 52, 58, 55, 60, 54, 59, 58, 58, 56, 58, 57, 58, 56, 52, 57,
          58, 55, 55, 59, 56, 57, 55, 58, 57, 56, 54, 56, 58, 57, 55, 56, 57,
          58, 55, 56, 57, 55, 56, 57, 55, 58, 56, 57, 58, 55, 56, 57, 56, 55,
          57, 58,
        ],
      },
      sessionHours: {
        "Rebecca McDonnell": [34, 32, 30, 36, 32, 30, 34, 36, 32, 30, 34, 32],
        "Elly Messo": [28, 30, 32, 28, 30, 32, 28, 30, 28, 32, 30, 28],
        "Lorena Halpin-Doyle": [26, 28, 30, 26, 28, 30, 26, 28, 30, 26, 28, 30],
        "Joanne Barnuevo": [22, 24, 20, 22, 24, 22, 24, 20, 22, 24, 22, 24],
        "All Counsellors": [
          110, 114, 112, 112, 114, 114, 112, 114, 112, 112, 114, 114,
        ],
      },
      sessionHoursWeekly: {
        "Rebecca McDonnell": [
          25, 26, 27, 28, 25, 26, 27, 28, 29, 30, 31, 32, 33, 31, 32, 31, 32,
          33, 30, 29, 30, 32, 33, 31, 30, 29, 30, 32, 33, 31, 30, 28, 27, 28,
          29, 30, 31, 32, 33, 31, 30, 32, 33, 31, 30, 29, 31, 32, 33, 30, 29,
          31,
        ],
        "Elly Messo": [
          22, 23, 24, 22, 23, 24, 25, 23, 24, 23, 22, 21, 22, 23, 24, 25, 23,
          24, 25, 24, 23, 23, 22, 23, 24, 25, 23, 24, 22, 21, 22, 23, 24, 22,
          23, 24, 23, 22, 21, 22, 23, 24, 25, 23, 22, 24, 25, 23, 24, 22, 21,
          22,
        ],
        "Lorena Halpin-Doyle": [
          18, 20, 21, 22, 19, 20, 21, 22, 21, 19, 18, 22, 20, 21, 22, 19, 21,
          20, 21, 20, 22, 21, 20, 19, 22, 21, 22, 19, 20, 21, 20, 22, 21, 19,
          20, 21, 22, 19, 20, 21, 22, 19, 20, 21, 22, 19, 20, 22, 21, 20, 21,
          22,
        ],
        "Joanne Barnuevo": [
          15, 16, 17, 18, 17, 16, 15, 16, 17, 18, 19, 20, 19, 20, 18, 17, 18,
          19, 20, 18, 19, 20, 19, 18, 16, 17, 18, 17, 19, 20, 21, 20, 19, 18,
          19, 20, 18, 17, 16, 18, 17, 19, 20, 21, 22, 20, 19, 21, 20, 19, 18,
          17,
        ],
        "All Counsellors": [
          80, 85, 88, 90, 85, 88, 90, 92, 95, 98, 100, 102, 105, 106, 107, 108,
          110, 112, 114, 116, 118, 120, 122, 124, 126, 128, 130, 132, 134, 136,
          138, 140, 142, 144, 146, 148, 150, 152, 154, 156, 158, 160, 162, 164,
          166, 168, 170, 172, 174, 176, 178, 180, 182, 184,
        ],
      },
      ratingMonthlyScores: {
        "Rebecca McDonnell": [
          9.0, 8.8, 8.6, 9.2, 8.8, 8.6, 9.0, 9.2, 8.8, 8.6, 9.0, 8.8,
        ],
        "Elly Messo": [
          8.6, 8.8, 9.0, 8.6, 8.8, 9.0, 8.6, 8.8, 8.6, 9.0, 8.8, 8.6,
        ],
        "Lorena Halpin-Doyle": [
          8.4, 8.6, 8.8, 8.4, 8.6, 8.8, 8.4, 8.6, 8.8, 8.4, 8.6, 8.8,
        ],
        "Joanne Barnuevo": [
          9.8, 10.0, 9.8, 10.0, 9.8, 10.0, 9.8, 10.0, 9.8, 10.0, 9.8, 10.0,
        ],
        "All Counsellors": [
          8.4, 8.5, 8.4, 8.4, 8.5, 8.5, 8.4, 8.5, 8.4, 8.4, 8.5, 8.5,
        ],
      },
      ratingWeeklyScores: {
        "Rebecca McDonnell": [
          8.4, 8.5, 8.6, 8.7, 8.4, 8.5, 8.6, 8.7, 8.8, 8.9, 9.0, 9.1, 9.2, 9.0,
          9.1, 9.0, 9.1, 9.2, 8.9, 8.8, 8.9, 9.1, 9.2, 9.0, 8.9, 8.8, 8.9, 9.1,
          9.2, 9.0, 8.9, 8.7, 8.6, 8.7, 8.8, 8.9, 9.0, 9.1, 9.2, 9.0, 8.9, 9.1,
          9.2, 9.0, 8.9, 8.8, 9.0, 9.1, 9.2, 9.0, 8.9, 9.1,
        ],
        "Elly Messo": [
          8.2, 8.3, 8.4, 8.2, 8.3, 8.4, 8.5, 8.3, 8.4, 8.3, 8.2, 8.1, 8.2, 8.3,
          8.4, 8.5, 8.3, 8.4, 8.5, 8.4, 8.3, 8.3, 8.2, 8.3, 8.4, 8.5, 8.3, 8.4,
          8.2, 8.1, 8.2, 8.3, 8.4, 8.2, 8.3, 8.4, 8.3, 8.2, 8.1, 8.2, 8.3, 8.4,
          8.5, 8.3, 8.2, 8.4, 8.5, 8.3, 8.4, 8.2, 8.1, 8.2,
        ],
        "Lorena Halpin-Doyle": [
          7.8, 8.0, 8.1, 8.2, 7.9, 8.0, 8.1, 8.2, 8.1, 7.9, 7.8, 8.2, 8.0, 8.1,
          8.2, 7.9, 8.1, 8.0, 8.1, 8.0, 8.2, 8.1, 8.0, 7.9, 8.2, 8.1, 8.2, 7.9,
          8.0, 8.1, 8.0, 8.2, 8.1, 7.9, 8.0, 8.1, 8.2, 7.9, 8.0, 8.1, 8.2, 7.9,
          8.0, 8.1, 8.2, 7.9, 8.0, 8.2, 8.1, 8.0, 8.1, 8.2,
        ],
        "Joanne Barnuevo": [
          9.5, 9.6, 9.7, 9.8, 9.7, 9.6, 9.5, 9.6, 9.7, 9.8, 9.9, 10.0, 9.9,
          10.0, 9.8, 9.7, 9.8, 9.9, 10.0, 9.8, 9.9, 10.0, 9.9, 9.8, 9.6, 9.7,
          9.8, 9.7, 9.9, 10.0, 10.1, 10.0, 9.9, 9.8, 9.9, 10.0, 9.8, 9.7, 9.6,
          9.8, 9.7, 9.9, 10.0, 10.1, 10.2, 10.0, 9.9, 10.1, 10.0, 9.9, 9.8, 9.7,
        ],
        "All Counsellors": [
          8.0, 8.5, 8.8, 9.0, 8.5, 8.8, 9.0, 9.2, 9.5, 9.7, 9.9, 10.0, 9.9, 9.8,
          9.8, 9.7, 9.8, 9.9, 9.9, 9.8, 9.7, 9.8, 9.9, 10.0, 9.9, 9.8, 9.9, 9.9,
          9.8, 9.7, 9.6, 9.7, 9.8, 9.9, 10.0, 9.9, 9.8, 9.7, 9.8, 9.9, 9.9, 9.8,
          9.7, 9.8, 9.9, 10.0, 9.9, 9.8, 9.9, 10.0, 9.9, 9.8, 9.9,
        ],
      },
      viewType: "monthly",
    };
  },
  watch: {
    selectedCounsellor: {
      immediate: true,
      handler(newVal) {
        this.clientChartCounts = this.totalClients[newVal] || [];
        this.selectedSessionHours = this.sessionHours[newVal] || [];
      },
    },
  },
};
</script>

<style>
body {
  margin: 0;
  font-family: "Inter", sans-serif;
  background-color: #f9f9f9;
}

#app {
  width: 100%;
  min-height: 100vh;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
}

.content-wrapper {
  display: flex;
  justify-content: center;
  flex: 1;
}

.main-content {
  width: 100%;
  max-width: 1400px;
  padding: 15px 20px;
  box-sizing: border-box;
}

h3 {
  font-size: 17px;
  font-weight: 400;
  color: #0f3659;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
  margin-bottom: 15px;
}

.counselling-heading {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  padding: 10px 0;
}

.dropdowns {
  display: flex;
  flex-direction: row;
  gap: 15px;
  justify-content: flex-end;
}

.dropdown-container {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  margin-bottom: 15px;
  margin-top: 15px;
}

.dropdown-container select {
  font-family: "Assistant";
  padding: 8px 4px;
  outline: none;
  border-radius: 4px;
  font-size: 14px;
}

.graph-container {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 15px;
}

@media (max-width: 1200px) {
  .stats-grid {
    grid-template-columns: 1fr 1fr;
  }
}

@media (max-width: 900px) {
  .graph-container {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 700px) {
  .stats-grid {
    grid-template-columns: 1fr;
  }
}
</style>
