<template>
  <div>
    <SearchBar @filter-by-name="filterParticipants" />
    <table class="table table-striped">
      <thead>
      <tr>
        <th @click="sortBy('name')" style="cursor: pointer">
          Name
          <span v-if="sortKey === 'name'">{{ sortOrder === 1 ? '▲' : '▼' }}</span>
        </th>
        <th @click="sortBy('dateOfBirth')" style="cursor: pointer">
          Date of Birth
          <span v-if="sortKey === 'dateOfBirth'">{{ sortOrder === 1 ? '▲' : '▼' }}</span>
        </th>
        <th>Email</th>
        <th>Phone number</th>
        <th>Actions</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(participant, index) in sortedParticipants" :key="index">
        <td>{{ participant.name }}</td>
        <td>{{ participant.dateOfBirth }}</td>
        <td>{{ participant.email }}</td>
        <td>{{ participant.phoneNumber }}</td>
        <td>
          <CustomButton text="Edit" :clickHandler="() => editParticipant(index)" />
          <CustomButton text="Delete" :clickHandler="() => deleteParticipant(index)" />
        </td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from "vue";
import type { Participant } from "@/models/Participant";
import SearchBar from "./SearchBar.vue";
import CustomButton from "./CustomButton.vue";

export default defineComponent({
  name: "ParticipantsTable",
  components: { SearchBar, CustomButton },
  props: {
    participants: { type: Array as () => Participant[], required: true },
  },
  emits: ["edit-participant", "delete-participant"],
  setup(props, { emit }) {
    const searchQuery = ref("");
    const sortKey = ref<keyof Participant | null>(null);
    const sortOrder = ref(1); // 1 for ascending, -1 for descending

    const sortedParticipants = computed(() => {
      const filtered = [...props.participants].filter((p) =>
        p.name.toLowerCase().includes(searchQuery.value.toLowerCase())
      );

      if (sortKey.value) {
        filtered.sort((a, b) => {
          if (a[sortKey.value!] > b[sortKey.value!]) return sortOrder.value;
          if (a[sortKey.value!] < b[sortKey.value!]) return -sortOrder.value;
          return 0;
        });
      }
      return filtered;
    });

    const filterParticipants = (query: string) => {
      searchQuery.value = query;
    };

    const sortBy = (key: keyof Participant) => {
      if (sortKey.value === key) {
        sortOrder.value *= -1; // Toggle sort order
      } else {
        sortKey.value = key;
        sortOrder.value = 1;
      }
    };

    const editParticipant = (index: number) => {
      emit("edit-participant", index);
    };

    const deleteParticipant = (index: number) => {
      emit("delete-participant", index);
    };

    return {
      sortedParticipants,
      filterParticipants,
      sortBy,
      editParticipant,
      deleteParticipant,
      sortKey,
      sortOrder,
    };
  },
});
</script>

<style scoped>
.table th {
  cursor: pointer;
}
</style>
