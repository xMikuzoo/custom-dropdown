<script setup lang="ts">
import CustomDropdown from "./components/CustomDropdown.vue";

const options = [
  { label: "Option 1", value: "option1" },
  { label: "Option 2", value: "option2" },
  { label: "Option 3", value: "option3" },
];

function handleOptionSelected(option) {
  console.log("Selected option:", option);
}
</script>

<template>
  <div class="app-container">
    <h1>Custom Dropdown Example</h1>
    <CustomDropdown :options="options" @optionSelected="handleOptionSelected" />
  </div>
</template>
<style>
.app-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f0f2f5;
  padding: 20px;
  box-sizing: border-box;
}

h1 {
  color: #333;
  margin-bottom: 2rem;
}
</style>
