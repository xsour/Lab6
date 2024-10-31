<template>
  <form @submit.prevent="register">
    <InputField
      label="Name"
      v-model="newParticipant.name"
      placeholder="Enter user name"
      :error="errors.name"
    />
    <InputField
      label="Date of Birth"
      v-model="newParticipant.dateOfBirth"
      type="date"
      :error="errors.dateOfBirth"
    />
    <InputField
      label="Email"
      v-model="newParticipant.email"
      placeholder="Enter email"
      :error="errors.email || emailError"
    />
    <InputField
      label="Phone number"
      v-model="newParticipant.phoneNumber"
      placeholder="Enter phone number"
      :error="errors.phoneNumber"
    />
    <button type="submit" class="btn btn-primary">Save</button>
    <div class="text-danger" v-if="emailError">{{ emailError }}</div>
  </form>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import type { PropType } from "vue";
import InputField from "./InputField.vue";
import { Validator } from "@/misc/Validator";
import type { Participant } from "@/models/Participant";

export default defineComponent({
  name: "RegistrationForm",
  components: { InputField },
  props: {
    participants: {
      type: Array as PropType<Participant[]>,
      required: true,
    },
  },
  emits: ["register-participant"],
  setup(props, { emit }) {
    const newParticipant = ref({
      name: "",
      dateOfBirth: "",
      email: "",
      phoneNumber: "",
    });
    const errors = ref({
      name: "",
      dateOfBirth: "",
      email: "",
      phoneNumber: "",
    });
    const emailError = ref(""); // Додаємо змінну для відстеження помилки email

    const validateForm = () => {
      emailError.value = ""; // Скидаємо emailError перед перевіркою
      errors.value.name = Validator.validateName(newParticipant.value.name);
      errors.value.dateOfBirth = Validator.validateDateOfBirth(
        newParticipant.value.dateOfBirth,
        new Date().toISOString().split("T")[0]
      );
      errors.value.email = Validator.validateEmail(newParticipant.value.email);
      errors.value.phoneNumber = Validator.validatePhoneNumber(
        newParticipant.value.phoneNumber
      );

      return !Object.values(errors.value).some((error) => error) && !emailError.value;
    };

    const register = () => {
      // Перевірка на унікальність email
      if (props.participants.some((p) => p.email === newParticipant.value.email)) {
        emailError.value = "Email already exists!";
        return;
      }

      if (validateForm()) {
        emit("register-participant", { ...newParticipant.value });
        newParticipant.value = {
          name: "",
          dateOfBirth: "",
          email: "",
          phoneNumber: "",
        };
        errors.value = {
          name: "",
          dateOfBirth: "",
          email: "",
          phoneNumber: "",
        };
        emailError.value = ""; // Скидаємо помилку після успішного додавання
      }
    };

    return { newParticipant, errors, emailError, register };
  },
});
</script>
