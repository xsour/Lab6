<template>
  <div class="winner-block d-flex align-items-center">
    <div class="winner-tags gray-border flex-grow-1">
      <WinnerBadge
        v-for="(winner, index) in winners"
        :key="index"
        :winner="winner"
        :index="index"
        @remove-winner="removeWinner"
      />
      <span class="badge">Winners</span>
    </div>
    <CustomButton
      text="New winner"
      :clickHandler="selectWinner"
      :buttonClass="'btn btn-primary ml-3'"
      :disabled="winners.length >= 3 || participants.length === 0"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import type { Participant } from "@/models/Participant";
import WinnerBadge from "./WinnerBadge.vue";
import CustomButton from "./CustomButton.vue"; // Змінено імпорт на CustomButton

export default defineComponent({
  name: "WinnersBlock",
  components: { WinnerBadge, CustomButton },
  props: {
    winners: { type: Array as () => Participant[], required: true },
    participants: { type: Array as () => Participant[], required: true },
  },
  emits: ["select-winner", "remove-winner"],
  methods: {
    selectWinner() {
      this.$emit("select-winner");
    },
    removeWinner(index: number) {
      this.$emit("remove-winner", index);
    },
  },
});
</script>

<style scoped>
.winner-block {
  border: 1px solid #dee2e6;
  border-radius: 4px;
  padding: 1rem;
}
.gray-border {
  border: solid gray 1px;
  border-radius: 6px;
  padding: 10px;
}
</style>
