<script setup>
import { ref } from "vue";

// Emits
const emit = defineEmits(["add-task"]);

// Local state
const newTask = ref("");
const taskDescription = ref("");
const dueDate = ref("");
const priority = ref("Low");

// Methods
const submitTask = () => {
  if (newTask.value.trim() !== "") {
    emit("add-task", {
      text: newTask.value,
      description: taskDescription.value,
      dueDate: dueDate.value,
      priority: priority.value,
      completed: false,
    });
    newTask.value = "";
    taskDescription.value = "";
    dueDate.value = "";
    priority.value = "Low";
  }
};
</script>

<template>
  <form @submit.prevent="submitTask" class="todo-input-form">
    <div>
      <label for="newTask">Name your task </label>
      <input
        v-model="newTask"
        placeholder="Add a new task"
        class="todo-input" />
    </div>

    <!-- Task Description -->
    <div>
      <label for="taskDescription">Task Description</label>
      <input
        v-model="taskDescription"
        placeholder="Add a description"
        class="todo-description-input" />
    </div>

    <!-- Due Date -->
    <div class="task-details">
      <label for="dueDate">Due Date</label>
      <input v-model="dueDate" type="date" class="todo-date" />

      <!-- Priority -->
      <label for="priority">Priority</label>
      <select v-model="priority" class="todo-priority">
        <option value="Low">Low</option>
        <option value="Medium">Medium</option>
        <option value="High">High</option>
      </select>
    </div>

    <button type="submit" class="add-task-button">Add task</button>
  </form>
</template>

<style scoped>
.todo-input-form {
  display: grid;
  margin-bottom: 20px;
}

.todo-input-form label {
  margin-bottom: 5px;
}

.task-details {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  align-items: center;
  margin-bottom: 20px;
  margin-top: 20px;
}

.todo-input {
  flex: 2;
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

.todo-description-input {
  flex: 2;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  outline: none;
  transition: border-color 0.3s;
}

.todo-description-input:focus {
  border-color: #007bff;
}

.todo-date,
.todo-priority {
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-left: 10px;
  outline: none;
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
