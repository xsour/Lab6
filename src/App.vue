<template>
  <div class="lottery-app">
    <!-- Winners Block -->
    <div class="card">
      <div class="d-flex align-items-center">
        <div class="winner-tags gray-border flex-grow-1">
          <span
            v-for="(winner, index) in winners"
            :key="index"
            class="badge blue"
          >
            {{ winner.name }}
            <button @click="removeWinner(index)" class="btn btn-sm btn-danger">
              &times;
            </button>
          </span>
          <span class="badge">Winners</span>
        </div>
        <button
          class="btn btn-primary ml-3"
          :disabled="winners.length >= 3 || participants.length === 0"
          @click="selectWinner"
        >
          New winner
        </button>
      </div>
    </div>

    <!-- Registration Form -->
    <div class="card">
      <h3>REGISTER FORM</h3>
      <p>Please fill in all the fields.</p>
      <form @submit.prevent="registerParticipant" novalidate>
        <div class="form-group">
          <label>Name</label>
          <input
            v-model="newParticipant.name"
            type="text"
            class="form-control"
            placeholder="Enter user name"
            :class="{ 'is-invalid': nameError }"
            required
          />
          <div class="text-danger" v-if="nameError">{{ nameError }}</div>
        </div>
        <div class="form-group">
          <label>Date of Birth</label>
          <input
            v-model="newParticipant.dateOfBirth"
            type="date"
            class="form-control"
            :max="today"
            :class="{ 'is-invalid': dateError }"
            required
          />
          <div class="text-danger" v-if="dateError">{{ dateError }}</div>
        </div>
        <div class="form-group">
          <label>Email</label>
          <input
            v-model="newParticipant.email"
            type="email"
            class="form-control"
            placeholder="Enter email"
            :class="{ 'is-invalid': emailError }"
            required
          />
          <div class="text-danger" v-if="emailError">{{ emailError }}</div>
        </div>
        <div class="form-group">
          <label>Phone number</label>
          <input
            v-model="newParticipant.phoneNumber"
            type="tel"
            class="form-control"
            placeholder="Enter phone number"
            :class="{ 'is-invalid': phoneError }"
            required
          />
          <div class="text-danger" v-if="phoneError">{{ phoneError }}</div>
        </div>
        <div class="button-container">
          <button type="submit" class="btn btn-primary">Save</button>
        </div>
      </form>
    </div>

    <!-- Participants Table -->
    <div class="card">
      <table class="table table-striped">
        <thead>
        <tr>
          <th>#</th>
          <th>Name</th>
          <th>Date of Birth</th>
          <th>Email</th>
          <th>Phone number</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="(participant, index) in participants" :key="index">
          <td>{{ index + 1 }}</td>
          <td>{{ participant.name }}</td>
          <td>{{ participant.dateOfBirth }}</td>
          <td>{{ participant.email }}</td>
          <td>{{ participant.phoneNumber }}</td>
        </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import { Validator } from "@/misc/Validator";
import type { Participant } from "@/models/Participant";

export default defineComponent({
  name: "LotteryApp",
  setup() {
    const today = new Date().toISOString().split("T")[0]; // Current date
    const newParticipant = ref<Participant>({
      name: "",
      dateOfBirth: "",
      email: "",
      phoneNumber: "",
    });

    const participants = ref<Participant[]>([]);
    const winners = ref<Participant[]>([]);

    // Error messages
    const nameError = ref("");
    const dateError = ref("");
    const emailError = ref("");
    const phoneError = ref("");

    const registerParticipant = () => {
      nameError.value = Validator.validateName(newParticipant.value.name);
      dateError.value = Validator.validateDateOfBirth(
        newParticipant.value.dateOfBirth,
        today
      );
      emailError.value = Validator.validateEmail(newParticipant.value.email);
      phoneError.value = Validator.validatePhoneNumber(
        newParticipant.value.phoneNumber
      );

      if (
        nameError.value ||
        dateError.value ||
        emailError.value ||
        phoneError.value
      ) {
        return;
      }

      participants.value.push({ ...newParticipant.value });
      newParticipant.value.name = "";
      newParticipant.value.dateOfBirth = "";
      newParticipant.value.email = "";
      newParticipant.value.phoneNumber = "";
    };

    const selectWinner = () => {
      if (participants.value.length > 0 && winners.value.length < 3) {
        const randomIndex = Math.floor(
          Math.random() * participants.value.length
        );
        const winner = participants.value[randomIndex];
        winners.value.push(winner);
      }
    };

    const removeWinner = (index: number) => {
      winners.value.splice(index, 1);
    };

    return {
      newParticipant,
      participants,
      winners,
      nameError,
      dateError,
      emailError,
      phoneError,
      today,
      registerParticipant,
      selectWinner,
      removeWinner,
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
  margin: 20px auto 0 auto;
}

.card {
  border: 1px solid #dee2e6;
  border-radius: 4px;
  background-color: #ffffff;
  padding: 3%;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.d-flex {
  display: flex;
  flex-direction: row;
  align-items: center;
}

.winner-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  flex-grow: 1;
}

.winner-tags .badge {
  display: flex;
  color: black;
  height: 35px;
  align-items: center;
  gap: 5px;
  padding: 10px;
  font-size: 1rem;
}

.blue {
  background-color: rgb(213, 213, 213);
}

.winner-tags button {
  margin-left: 2px;
  margin-top: 1px;
  background-color: #00afdc;
  border-color: #00afdc;
  border-radius: 2px;
}

.gray-border {
  border: solid gray 1px;
  border-radius: 6px;
  padding: 10px;
}

.form-group {
  margin-bottom: 10px;
}

.text-danger {
  color: red;
  font-size: 0.875rem;
}

table {
  width: 100%;
}

thead {
  background-color: #e3e3e3;
}

th,
td {
  padding: 8px;
  text-align: center;
}

button {
  margin-top: 10px;
  background-color: #00afdc;
  border-color: #00afdc;
  border-radius: 2px;
}

button.btn.btn-primary {
  background-color: #00afdc;
  border-color: #00afdc;
  border-radius: 2px;
}

.button-container {
  display: flex;
  justify-content: flex-end;
}

.ml-3 {
  margin-left: 1rem;
}

</style>
