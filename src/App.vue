<template>
  <div id="app">
    <TopNav />
    <div class="content-wrapper">
      <div class="main-content">
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

        <!-- Graph -->
        <div class="graph-container">
          <GraphCard :title="`Total Clients – ${selectedCounsellor}`">
            <TotalClientsChart
              :labels="clientChartLabels"
              :counts="totalClients[selectedCounsellor]"
            />
          </GraphCard>

          <GraphCard :title="`Total Session Hours – ${selectedCounsellor}`">
            <TotalSessionHoursChart
              :labels="clientChartLabels"
              :session-hours="sessionHours[selectedCounsellor]"
            />
          </GraphCard>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import TopNav from "./components/Navigation/TopNav.vue";
import StatisticsCard from "./components/UI/StatisticsCard.vue";
import GraphCard from "./components/UI/GraphCard.vue";
import TotalClientsChart from "./components/Graphs/TotalClientsGraph.vue";
import TotalSessionHoursChart from "./components/Graphs/TotalSessionHoursGraph.vue";

export default {
  name: "App",
  components: {
    TopNav,
    StatisticsCard,
    GraphCard,
    TotalClientsChart,
    TotalSessionHoursChart,
  },
  data() {
    return {
      selectedCounsellor: "All Counsellors",
      counsellorData: {
        "All Counsellors": "All Counsellors",
        "Rebecca McDonnell": "Rebecca McDonnell",
        "Elly Messo": "Elly Messo",
        "Lorena  Halpin-Doyle": "Lorena  Halpin-Doyle",
        "Joanne Barnuevo": "Joanne Barnuevo",
      },
      stats: [
        {
          iconClass: "fas fa-user-friends",
          statName: "Students In Counselling",
          value: "45",
          iconColor: "#28c76f",
          iconContainerColor: "rgba(40, 199, 111, 0.1)",
        },
        {
          iconClass: "fas fa-ban",
          statName: "Cancellations",
          value: "15%",
          iconColor: "#ff6f91",
          iconContainerColor: "rgba(255, 111, 145, 0.1)",
        },
        {
          iconClass: "fas fa-clock",
          statName: "Wait List",
          value: "6",
          iconColor: "#ea5455",
          iconContainerColor: "rgba(234, 84, 85, 0.1)",
        },
        {
          iconClass: "fas fa-chair",
          statName: "Available Slots",
          value: "5",
          iconColor: "#00cfe8",
          iconContainerColor: "rgba(0, 207, 232, 0.1)",
        },

        {
          iconClass: "fas fa-user-clock",
          statName: "Average Wait Time",
          value: "12 days",
          iconColor: "#7367f0",
          iconContainerColor: "rgba(115, 103, 240, 0.1)",
        },
        {
          iconClass: "fas fa-hand-paper",
          statName: "Total Students Who Accessed Counselling",
          value: "12",
          iconColor: "#ff9f43",
          iconContainerColor: "rgba(255, 159, 67, 0.1)",
        },
        // {
        //   iconClass: "fas fa-users-cog",
        //   statName: "Avg Students per Counsellor",
        //   value: "11.3",
        //   iconColor: "#00cfe8",
        //   iconContainerColor: "rgba(0, 207, 232, 0.1)",
        // },

        // {
        //   iconClass: "fas fa-user-check",
        //   statName: "Avg Sessions per Student",
        //   value: "3.8",
        //   iconColor: "#1bc98e",
        //   iconContainerColor: "rgba(27, 201, 142, 0.1)",
        // },
      ],
      clientChartLabels: [
        "May",
        "Jun",
        "Jul",
        "Aug",
        "Sep",
        "Oct",
        "Nov",
        "Dec",
        "Jan",
        "Feb",
        "Mar",
        "Apr",
      ],
      totalClients: {
        "Rebecca McDonnell": [17, 16, 15, 18, 16, 15, 17, 18, 16, 15, 17, 16],
        "Elly Messo": [14, 15, 16, 14, 15, 16, 14, 15, 14, 16, 15, 14],
        "Lorena  Halpin-Doyle": [
          13, 14, 15, 13, 14, 15, 13, 14, 15, 13, 14, 15,
        ],
        "Joanne Barnuevo": [11, 12, 10, 11, 12, 11, 12, 10, 11, 12, 11, 12],
        "All Counsellors": [55, 57, 56, 56, 57, 57, 56, 57, 56, 56, 57, 57],
      },
      sessionHours: {
        "Rebecca McDonnell": [34, 32, 30, 36, 32, 30, 34, 36, 32, 30, 34, 32],
        "Elly Messo": [28, 30, 32, 28, 30, 32, 28, 30, 28, 32, 30, 28],
        "Lorena  Halpin-Doyle": [
          26, 28, 30, 26, 28, 30, 26, 28, 30, 26, 28, 30,
        ],
        "Joanne Barnuevo": [22, 24, 20, 22, 24, 22, 24, 20, 22, 24, 22, 24],
        "All Counsellors": [
          110, 114, 112, 112, 114, 114, 112, 114, 112, 112, 114, 114,
        ],
      },
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
  font-family: "Assistant", sans-serif;
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

.stats-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
  margin-bottom: 15px;
}

.dropdown-container {
  display: flex;
  align-items: center;
  gap: 10px;
  width: 100%;
  justify-content: flex-end;
  margin-bottom: 15px;
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
