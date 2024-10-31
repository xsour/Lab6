<template>
  <div class="lottery-app">
    <!-- Winners Block -->
    <WinnersBlock
      :winners="winners"
      :participants="participants"
      @select-winner="selectWinner"
      @remove-winner="removeWinner"
    />

    <!-- Registration Form -->
    <div class="card">
      <h3>REGISTER FORM</h3>
      <RegistrationForm
        :participants="participants"
      @register-participant="registerParticipant"
      />
    </div>

    <!-- Participants Table -->
    <div class="card">
      <ParticipantsTable
        :participants="participants"
        @edit-participant="openEditModal"
        @delete-participant="openDeleteModal"
      />
    </div>

    <!-- Modal for Editing -->
    <CustomModal v-if="showEditModal" @close="closeEditModal">
      <template #header>Edit Participant</template>
      <template #body>
        <RegistrationForm
          :initial-data="editingParticipant"
          :participants="participants"
        @register-participant="updateParticipant"
        />
      </template>
      <template #footer>
        <CustomButton text="Cancel" :clickHandler="closeEditModal" />
      </template>
    </CustomModal>

    <!-- Modal for Deletion Confirmation -->
    <CustomModal v-if="showDeleteModal" @close="closeDeleteModal">
      <template #header>Confirm Delete</template>
      <template #body>
        Are you sure you want to delete {{ deletingParticipant?.name }}?
      </template>
      <template #footer>
        <CustomButton text="Yes" :clickHandler="deleteParticipant" />
        <CustomButton text="No" :clickHandler="closeDeleteModal" />
      </template>
    </CustomModal>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from "vue";
import WinnersBlock from "@/components/WinnersBlock.vue";
import RegistrationForm from "@/components/RegistrationForm.vue";
import ParticipantsTable from "@/components/ParticipantsTable.vue";
import CustomModal from "@/components/CustomModal.vue";
import CustomButton from "@/components/CustomButton.vue";
import type { Participant } from "@/models/Participant";

export default defineComponent({
  name: "LotteryApp",
  components: {
    WinnersBlock,
    RegistrationForm,
    ParticipantsTable,
    CustomModal,
    CustomButton,
  },
  setup() {
    const today = new Date().toISOString().split("T")[0];
    const participants = ref<Participant[]>([]);

    // Load participants from localStorage
    try {
      const storedParticipants = localStorage.getItem("participants");
      participants.value = storedParticipants ? JSON.parse(storedParticipants) : [];
    } catch (error) {
      console.error("Error loading participants from localStorage. Clearing storage.", error);
      localStorage.removeItem("participants");
    }

    const winners = ref<Participant[]>([]);
    const showEditModal = ref(false);
    const showDeleteModal = ref(false);
    const editingParticipant = ref<Participant | undefined>(undefined);
    const deletingParticipant = ref<Participant | undefined>(undefined);

    // Watch participants and save to localStorage
    watch(
      participants,
      (newParticipants) => {
        localStorage.setItem("participants", JSON.stringify(newParticipants));
      },
      { deep: true }
    );

    // Register a new participant
    const registerParticipant = (participant: Participant) => {
      const emailExists = participants.value.some((p) => p.email === participant.email);
      if (emailExists) {
        alert("Email already exists!"); // Replace with a custom error message in UI
        return;
      }
      participants.value.push(participant);
    };

    // Select a random winner
    const selectWinner = () => {
      if (participants.value.length > 0 && winners.value.length < 3) {
        const randomIndex = Math.floor(Math.random() * participants.value.length);
        const winner = participants.value[randomIndex];
        winners.value.push(winner);
      }
    };

    // Remove a winner
    const removeWinner = (index: number) => {
      winners.value.splice(index, 1);
    };

    // Open the edit modal
    const openEditModal = (index: number) => {
      editingParticipant.value = { ...participants.value[index] };
      showEditModal.value = true;
    };

    // Update participant information
    const updateParticipant = (updatedParticipant: Participant) => {
      const index = participants.value.findIndex((p) => p.email === editingParticipant.value?.email);
      if (index !== -1) {
        participants.value[index] = updatedParticipant;
      }
      closeEditModal();
    };

    // Close edit modal
    const closeEditModal = () => {
      showEditModal.value = false;
      editingParticipant.value = undefined;
    };

    // Open delete confirmation modal
    const openDeleteModal = (index: number) => {
      deletingParticipant.value = participants.value[index];
      showDeleteModal.value = true;
    };

    // Delete a participant
    const deleteParticipant = () => {
      if (deletingParticipant.value) {
        const index = participants.value.findIndex((p) => p.email === deletingParticipant.value?.email);
        if (index !== -1) {
          participants.value.splice(index, 1);
        }
      }
      closeDeleteModal();
    };

    // Close delete confirmation modal
    const closeDeleteModal = () => {
      showDeleteModal.value = false;
      deletingParticipant.value = undefined;
    };

    return {
      today,
      participants,
      winners,
      showEditModal,
      showDeleteModal,
      editingParticipant,
      deletingParticipant,
      registerParticipant,
      selectWinner,
      removeWinner,
      openEditModal,
      updateParticipant,
      closeEditModal,
      openDeleteModal,
      deleteParticipant,
      closeDeleteModal,
    };
  },
});
</script>

<style scoped>
.lottery-app {
  display: grid;
  grid-template-columns: 1fr;
  gap: 20px;
  max-width: 800px;
  margin: 20px auto;
}

.card {
  border: 1px solid #dee2e6;
  border-radius: 4px;
  background-color: #ffffff;
  padding: 3%;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
</style>
