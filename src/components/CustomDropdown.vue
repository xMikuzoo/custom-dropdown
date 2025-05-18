<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount, nextTick } from "vue";
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

const selectedOption = ref<DropdownOption | null>(null);
const isOpen = ref(false);
const dropdownButtonRef = ref<HTMLButtonElement | null>(null);
const dropdownMenuRef = ref<HTMLDivElement | null>(null);

const toggleDropdown = () => {
  isOpen.value = !isOpen.value;
};
const selectOption = (option: DropdownOption) => {
  selectedOption.value = option;
  isOpen.value = false;
  emit("optionSelected", option);
  nextTick(() => {
    dropdownButtonRef.value?.focus();
  });
};
const closeDropdown = (event: MouseEvent) => {
  const target = event.target as HTMLElement;
  if (isOpen.value && !target.closest(".dropdown")) {
    isOpen.value = false;
  }
};

onMounted(() => {
  document.addEventListener("click", closeDropdown);
});
onBeforeUnmount(() => {
  document.removeEventListener("click", closeDropdown);
});

const handleButtonKeyDown = (event: KeyboardEvent) => {
  if (event.key === "ArrowDown") {
    event.preventDefault();
    if (!isOpen.value) {
      isOpen.value = true;
    }
    nextTick(() => {
      const firstItem = dropdownMenuRef.value?.querySelector(
        '.dropdown-item[tabindex="0"]'
      ) as HTMLElement | null;
      firstItem?.focus();
    });
  } else if (event.key === "ArrowUp") {
    event.preventDefault();
    if (!isOpen.value) {
      isOpen.value = true;
    }
    nextTick(() => {
      const items = dropdownMenuRef.value?.querySelectorAll(
        '.dropdown-item[tabindex="0"]'
      ) as NodeListOf<HTMLElement> | null;
      if (items && items.length > 0) {
        items[items.length - 1]?.focus();
      }
    });
  } else if (isOpen.value && event.key === "Tab" && !event.shiftKey) {
    event.preventDefault();
    const firstItem = dropdownMenuRef.value?.querySelector(
      '.dropdown-item[tabindex="0"]'
    ) as HTMLElement | null;
    firstItem?.focus();
  } else if (event.key === "Escape") {
    if (isOpen.value) {
      event.preventDefault();
      isOpen.value = false;
      dropdownButtonRef.value?.focus();
    }
  }
};

const handleItemKeyDown = (
  event: KeyboardEvent,
  option: DropdownOption,
  index: number
) => {
  const items = Array.from(
    dropdownMenuRef.value?.querySelectorAll('.dropdown-item[tabindex="0"]') ||
      []
  ) as HTMLElement[];

  if (event.key === "Enter" || event.key === " ") {
    event.preventDefault();
    selectOption(option);
  } else if (event.key === "Escape") {
    event.preventDefault();
    isOpen.value = false;
    dropdownButtonRef.value?.focus();
  } else if (event.key === "Tab") {
    event.preventDefault();
    if (event.shiftKey) {
      if (index === 0) {
        dropdownButtonRef.value?.focus();
      } else {
        items[index - 1]?.focus();
      }
    } else {
      if (index === props.options.length - 1) {
        dropdownButtonRef.value?.focus();
      } else {
        items[index + 1]?.focus();
      }
    }
  } else if (event.key === "ArrowDown") {
    event.preventDefault();
    if (index < props.options.length - 1) {
      items[index + 1]?.focus();
    } else {
      items[0]?.focus();
    }
  } else if (event.key === "ArrowUp") {
    event.preventDefault();
    if (index > 0) {
      items[index - 1]?.focus();
    } else {
      items[items.length - 1]?.focus();
    }
  }
};
</script>

<template>
  <div class="dropdown-wrapper">
    <div class="dropdown">
      <button
        ref="dropdownButtonRef"
        class="dropdown-button"
        @click="toggleDropdown"
        @keydown="handleButtonKeyDown"
        aria-haspopup="listbox"
        :aria-expanded="isOpen.toString()"
      >
        {{ selectedOption ? selectedOption.label : "Select an option" }}
        <ChevronUp v-if="isOpen" />
        <ChevronDown v-else />
      </button>
      <div
        v-if="isOpen"
        class="dropdown-menu"
        ref="dropdownMenuRef"
        role="listbox"
        @keydown.esc.prevent="
          isOpen = false;
          dropdownButtonRef?.focus();
        "
      >
        <div
          v-for="(option, index) in props.options"
          :key="option.value"
          class="dropdown-item"
          role="option"
          :aria-selected="option === selectedOption"
          tabindex="0"
          @click="selectOption(option)"
          @keydown="handleItemKeyDown($event, option, index)"
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
  padding: 4px;
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
  border-radius: 6px;
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

.dropdown-item:focus,
.dropdown-item:focus-visible {
  outline: 2px solid #007bff;
  outline-offset: -1px;
  background-color: #f0f0f0;
  border-color: #007bff;
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
