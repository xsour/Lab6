<template>
  <div class="form-group">
    <label>{{ label }}</label>
    <input
      v-model="inputValue"
      :type="type"
      :placeholder="placeholder"
      :class="['form-control', { 'is-invalid': error }]"
      @input="updateValue"
    />
    <div class="text-danger" v-if="error">{{ error }}</div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from "vue";

export default defineComponent({
  name: "InputField",
  props: {
    label: { type: String, required: true },
    modelValue: { type: String, default: "" },
    type: { type: String, default: "text" },
    placeholder: { type: String, default: "" },
    error: { type: String, default: "" },
  },
  emits: ["update:modelValue"],
  setup(props, { emit }) {
    const inputValue = ref(props.modelValue);

    const updateValue = () => {
      emit("update:modelValue", inputValue.value);
    };

    watch(() => props.modelValue, (newVal) => {
      inputValue.value = newVal;
    });

    return { inputValue, updateValue };
  },
});
</script>

<style scoped>
.form-group {
  margin-bottom: 10px;
}
.text-danger {
  color: red;
  font-size: 0.875rem;
}
</style>
