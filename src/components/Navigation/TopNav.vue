<template>
  <nav>
    <div class="nav-container">
      <span class="title">Counselling Dashboard</span>
    </div>
  </nav>
</template>

<script>
export default {
  name: "TopNav",
};
</script>

<style scoped>
nav {
  width: 100%;
  height: 55px;
  background-color: #345a98;
  border-bottom: 1px solid #ccc;
  display: flex;
  justify-content: center;
  align-items: center;
}

.nav-container {
  width: 100%;
  max-width: 1400px;
}

.title {
  font-size: 18px;
  color: white;
  padding: 20px;
}
</style>
