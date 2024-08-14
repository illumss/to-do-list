<template>
  <form @submit.prevent="submitTask" class="todo-input-form">
    <input v-model="newTask" placeholder="Add a new task" class="todo-input" />
    <button type="submit" class="add-task-button">Add</button>
  </form>
</template>

<script setup>
import { ref } from "vue";

// Emits
const emit = defineEmits(["add-task"]);

// Local state
const newTask = ref("");

// Methods
const submitTask = () => {
  if (newTask.value.trim() !== "") {
    emit("add-task", newTask.value);
    newTask.value = "";
  }
};
</script>

<style scoped>
.todo-input-form {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.todo-input {
  flex: 1;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  outline: none;
  transition: border-color 0.3s;
}

.todo-input:focus {
  border-color: #007bff;
}

.add-task-button {
  padding: 10px 20px;
  margin-left: 10px;
  font-size: 16px;
  color: white;
  background-color: #007bff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.add-task-button:hover {
  background-color: #0056b3;
}

.add-task-button:active {
  background-color: #003f8a;
}
</style>
