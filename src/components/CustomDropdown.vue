<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from "vue";
import { Check, ChevronDown, ChevronUp } from "lucide-vue-next";

interface DropdownOption {
  label: string;
  value: string;
}

const emit = defineEmits<{
  (e: "optionSelected", option: DropdownOption): void;
}>();

const props = defineProps({
  options: {
    type: Array as () => DropdownOption[],
    required: true,
  },
});

const selectedOption = ref<DropdownOpiton | null>(null);
const isOpen = ref(false);

const toggleDropdown = () => {
  isOpen.value = !isOpen.value;
};
const selectOption = (option: DropdownOption) => {
  selectedOption.value = option;
  isOpen.value = false;
  emit("optionSelected", option);
};
const closeDropdown = (event: MouseEvent) => {
  if (isOpen.value && !event.target.closest(".dropdown")) {
    isOpen.value = false;
  }
};

onMounted(() => {
  document.addEventListener("click", closeDropdown);
});
onBeforeUnmount(() => {
  document.removeEventListener("click", closeDropdown);
});
</script>

<template>
  <div class="dropdown-wrapper">
    <div class="dropdown">
      <button class="dropdown-button" @click="toggleDropdown">
        {{ selectedOption ? selectedOption.label : "Select an option" }}
        <ChevronUp v-if="isOpen" />
        <ChevronDown v-else />
      </button>
      <div v-if="isOpen" class="dropdown-menu">
        <div
          v-for="option in props.options"
          :key="option"
          class="dropdown-item"
          @click="selectOption(option)"
        >
          <span> {{ option.label }}</span>
          <Check v-if="option === selectedOption" />
        </div>
      </div>
    </div>
  </div>
</template>
<style>
.dropdown-wrapper {
  position: relative;
  display: inline-block;
  width: 250px;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}

.dropdown {
  position: relative;
}

.dropdown-button {
  background-color: #ffffff;
  color: #333333;
  padding: 12px 18px;
  border: 1px solid #cccccc;
  border-radius: 6px;
  cursor: pointer;
  text-wrap: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  transition: background-color 0.2s ease-in-out, border-color 0.2s ease-in-out;
}

.dropdown-button:hover {
  background-color: #f9f9f9;
  border-color: #bbbbbb;
}

.dropdown-button:focus,
.dropdown-button:focus-visible {
  outline: 2px solid #007bff;
  outline-offset: 2px;
  border-color: #007bff;
}

.dropdown-menu {
  position: absolute;
  top: calc(100% + 6px);
  left: 0;
  background-color: #ffffff;
  border: 1px solid #dddddd;
  border-radius: 6px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  width: 100%;
  z-index: 1000;
  overflow: hidden;
}

.dropdown-item {
  padding: 12px 18px;
  cursor: pointer;
  border-bottom: 1px solid #eeeeee;
  display: flex;
  justify-content: space-between;
  align-items: center;
  text-align: left;
  color: #333333;
  transition: background-color 0.15s ease-in-out;
  font-size: 0.95em;
}

.dropdown-item:last-child {
  border-bottom: none;
}

.dropdown-item:hover {
  background-color: #f0f0f0;
}

.dropdown-item:active {
  background-color: #e0e0e0;
}

.dropdown-item span {
  flex-grow: 1;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.dropdown-item .lucide-check {
  color: #007bff;
  margin-left: 10px;
}
</style>
