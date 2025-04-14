<template>
  <div class="card">
    <div class="card-header">
      <div
        class="icon-container"
        :style="{ backgroundColor: iconContainerColor }"
      >
        <i :class="iconClass" :style="{ color: iconColor }"></i>
      </div>
      <span class="stat-name">{{ statName }}</span>
    </div>
    <div class="value">{{ value }}</div>
  </div>
</template>

<script>
export default {
  props: {
    iconClass: {
      type: String,
      required: true,
    },
    statName: {
      type: String,
      required: true,
    },
    value: {
      type: String,
      required: true,
    },
    iconColor: {
      type: String,
      default: "#28c76f",
    },
    iconContainerColor: {
      type: String,
      default: "rgba(39, 148, 97, 0.1)",
    },
  },
};
</script>

<style scoped>
.card {
  background: white;
  padding: 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  transition: background-color 0.1s linear;
  color: #345a98;
  border-radius: 5px;
  border: 1px solid #ccc;
  box-shadow: rgba(0, 0, 0, 0.04) 0px 1px 4px;
}

.card-header {
  font-family: "Assistant", sans-serif;
  display: flex;
  align-items: center;
  font-size: 15px;
  font-weight: 500;
}

.icon-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 32px;
  height: 32px;
  border-radius: 4px;
  margin-right: 7px;
}

.icon-container i {
  font-size: 18px;
}

.value {
  font-size: 15px;
  font-weight: 500;
  color: #333;
}
</style>
