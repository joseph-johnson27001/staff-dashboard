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
      </div>
    </div>
  </div>
</template>

<script>
import TopNav from "./components/Navigation/TopNav.vue";
import StatisticsCard from "./components/UI/StatisticsCard.vue";

export default {
  name: "App",
  components: {
    TopNav,
    StatisticsCard,
  },
  data() {
    return {
      stats: [
        {
          iconClass: "fas fa-user-friends",
          statName: "In Counselling",
          value: "45",
          iconColor: "#28c76f",
          iconContainerColor: "rgba(40, 199, 111, 0.1)",
        },
        {
          iconClass: "fas fa-hand-paper",
          statName: "Seeking Counselling",
          value: "12",
          iconColor: "#ff9f43",
          iconContainerColor: "rgba(255, 159, 67, 0.1)",
        },
        {
          iconClass: "fas fa-clock",
          statName: "Waitlisted",
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
      ],
    };
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
  padding: 20px;
  box-sizing: border-box;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
  margin-bottom: 2rem;
}
</style>
