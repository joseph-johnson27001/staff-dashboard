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
  align-items: center;
  background: white;
  padding: 15px;
  border-radius: 5px;
  box-shadow: rgba(50, 50, 93, 0.25) 0px 2px 5px -1px,
    rgba(0, 0, 0, 0.3) 0px 1px 3px -1px;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  transition: background-color 0.1s linear;
  color: #345a98;
}

.card-header {
  font-family: "Assistant", sans-serif;
  display: flex;
  align-items: center;
  font-size: 16px;
  font-weight: 500;
}

.icon-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 40px;
  height: 40px;
  border-radius: 4px;
  margin-right: 10px;
}

.icon-container i {
  font-size: 20px;
}

.stat-name {
  margin-right: 5px;
}

.value {
  font-size: 16px;
  font-weight: 500;
}
</style>
