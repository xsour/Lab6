<template>
  <button :class="buttonClass" @click="clickHandler">{{ text }}</button>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import type { PropType } from "vue";

export default defineComponent({
  name: "CustomButton",
  props: {
    text: { type: String, required: true },
    buttonClass: { type: String, default: "btn btn-primary" },
    clickHandler: { type: Function as PropType<() => void>, required: true },
  },
});
</script>

<style scoped>
button {
  border-radius: 4px;
  padding: 0.5rem 1rem;
}
</style>
