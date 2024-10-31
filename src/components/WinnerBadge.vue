<template>
  <span class="badge blue">
    {{ winner.name }}
    <button @click="remove" class="btn btn-sm btn-danger">
      &times;
    </button>
  </span>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import type { Participant } from "@/models/Participant";

export default defineComponent({
  name: "WinnerBadge",
  props: {
    winner: { type: Object as () => Participant, required: true },
    index: { type: Number, required: true },
  },
  emits: ["remove-winner"],
  methods: {
    remove() {
      this.$emit("remove-winner", this.index);
    },
  },
});
</script>

<style scoped>
.badge {
  display: flex;
  align-items: center;
  gap: 5px;
  padding: 10px;
  font-size: 1rem;
  background-color: rgb(213, 213, 213);
  border-radius: 4px;
  color: black;
}
</style>
