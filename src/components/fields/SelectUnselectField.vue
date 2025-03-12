<template>
  <div class="select-unselect-container">
    <div class="options-container">
      <h3>Available Options</h3>
      <div class="options-box">
        <div
          v-for="option in availableOptions"
          :key="option.id"
          class="option available"
          @click="toggleOption(option, false)"
        >
          {{ option.label }}
        </div>
      </div>
    </div>

    <div class="options-container">
      <h3>Disabled Options</h3>
      <div class="options-box">
        <div
          v-for="option in disabledOptions"
          :key="option.id"
          class="option disabled"
          @click="toggleOption(option, true)"
        >
          {{ option.label }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { defineProps, defineEmits, ref, watch } from "vue";

const props = defineProps({
  field: Object,
  modelValue: Array,
});

const emit = defineEmits(["update"]);

const availableOptions = ref(props.field.options);
const disabledOptions = ref([]);

// Alternar opciones entre listas
const toggleOption = (option, isDisabled) => {
  if (isDisabled) {
    disabledOptions.value = disabledOptions.value.filter(
      (o) => o.id !== option.id
    );
    availableOptions.value.push(option);
  } else {
    availableOptions.value = availableOptions.value.filter(
      (o) => o.id !== option.id
    );
    disabledOptions.value.push(option);
  }

  updateParent();
};

// Emitir los cambios al componente superior
const updateParent = () => {
  emit("update", {
    id: props.field.id,
    value: disabledOptions.value.map((o) => o.id),
  });
};

// Ver cambios en modelValue
watch(
  () => props.modelValue,
  (newValue) => {
    if (newValue) {
      disabledOptions.value = props.field.options.filter((o) =>
        newValue.includes(o.id)
      );
      availableOptions.value = props.field.options.filter(
        (o) => !newValue.includes(o.id)
      );
    }
  }
);
</script>

<style scoped>
.select-unselect-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  margin: 0 10px 10px 10px;
}

.options-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 45%;
}

h3 {
  text-align: center;
  margin: 10px;
  font-size: 18px;
  font-weight: bold;
}

.options-box {
  border: 1px solid #302e2e;
  padding: 10px;
  margin: 0 0 0 10px;
  width: 100%;
  min-height: 150px;
  display: flex;
  flex-direction: column;
  align-items: start;
  background-color: #1a1a1a;
  color: white;
  border-radius: 5px;
}
</style>
