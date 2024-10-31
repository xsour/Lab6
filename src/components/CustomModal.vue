<template>
  <div class="modal-backdrop" @click.self="close">
    <div class="modal-content">
      <header>
        <slot name="header">Default Header</slot>
        <button @click="close" class="btn-close">&times;</button>
      </header>
      <section>
        <slot name="body">Default Body</slot>
      </section>
      <footer>
        <slot name="footer">
          <CustomButton text="Close" :clickHandler="close" />
        </slot>
      </footer>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, onUnmounted } from "vue";
import CustomButton from "./CustomButton.vue";

export default defineComponent({
  name: "CustomModal",
  components: { CustomButton },
  emits: ["close"],
  setup(_, { emit }) {
    const close = () => {
      emit("close");
    };

    const handleEsc = (event: KeyboardEvent) => {
      if (event.key === "Escape") {
        close();
      }
    };

    onMounted(() => {
      window.addEventListener("keydown", handleEsc);
    });

    onUnmounted(() => {
      window.removeEventListener("keydown", handleEsc);
    });

    return { close };
  },
});
</script>

<style scoped>
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  background: white;
  padding: 20px;
  border-radius: 4px;
  min-width: 300px;
}

.btn-close {
  float: right;
  background: none;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
}
</style>
