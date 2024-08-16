<script setup>
import { ref } from "vue";

const emit = defineEmits(["add-task"]);

const newTask = ref("");
const taskDescription = ref("");
const dueDate = ref("");
const priority = ref("Low");

// Calculate today's date in YYYY-MM-DD format
const today = new Date().toISOString().split("T")[0];

const capitalizeFirstLetter = (string) => {
  return string.charAt(0).toUpperCase() + string.slice(1);
};

const submitTask = () => {
  if (newTask.value.trim() !== "") {
    emit("add-task", {
      text: capitalizeFirstLetter(newTask.value.trim()),
      description: capitalizeFirstLetter(taskDescription.value.trim()),
      dueDate: dueDate.value,
      priority: priority.value,
      completed: false,
    });
    // Reset form fields
    newTask.value = "";
    taskDescription.value = "";
    dueDate.value = "";
    priority.value = "Low";
  }
};
</script>

<template>
  <form @submit.prevent="submitTask" class="todo-input-form">
    <div class="form-group">
      <label for="newTask">Task Name</label>
      <input
        v-model="newTask"
        id="newTask"
        placeholder="Add a new task..."
        class="todo-input" />
    </div>

    <div class="form-group">
      <label for="taskDescription">Task Description</label>
      <input
        v-model="taskDescription"
        id="taskDescription"
        placeholder="Add a description..."
        class="todo-description-input" />
    </div>

    <div class="task-details">
      <div class="form-group">
        <label for="dueDate">Due Date</label>
        <input
          v-model="dueDate"
          id="dueDate"
          type="date"
          :min="today"
          class="todo-date" />
      </div>

      <div class="form-group">
        <div class="hover-state">
          <label for="priority">Priority *</label>
          <div class="hover-state-content">
            <p>
              Priority colors will be indicated as such, and refers to the
              importance of the task
            </p>
            <div class="hover-priority-details">
              <p class="low">Low</p>
              <p class="medium">Medium</p>
              <p class="high">High</p>
            </div>
          </div>
        </div>
        <select v-model="priority" id="priority" class="todo-priority">
          <option value="Low">Low</option>
          <option value="Medium">Medium</option>
          <option value="High">High</option>
        </select>
      </div>
    </div>

    <button type="submit" class="add-task-button">Add Task</button>
  </form>
</template>

<style scoped>
.todo-input-form {
  display: grid;
  gap: 20px;
  margin-bottom: 20px;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.task-details {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}

.todo-input,
.todo-description-input,
.todo-date,
.todo-priority {
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ffb3d9;
  border-radius: 4px;
  outline: none;
  transition: border-color 0.3s;
}

.todo-input:focus,
.todo-description-input:focus,
.todo-date:focus,
.todo-priority:focus {
  border-color: #ff66b2;
}

.add-task-button {
  padding: 10px 20px;
  font-size: 16px;
  color: #fff;
  background-color: #ff99cc;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
  transition: background-color 0.3s;
}

.add-task-button:hover {
  background-color: #ff66b2;
}

.hover-state {
  position: relative;
  display: inline-block;
}

.hover-state-content {
  display: none;
  position: absolute;
  background-color: #f9f9f9;
  box-shadow: 0px 8px 16px 0px rgba(0, 0, 0, 0.2);
  padding: 10px;
  border-radius: 4px;
  font-size: 13px;
  z-index: 1;
}

.hover-priority-details {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  flex-direction: space-between;
  gap: 5px;
}

.hover-priority-details p {
  border-radius: 4px;
  text-align: center;
}

.low {
  background-color: #4dff4d;
}

.medium {
  background-color: #ffcc00;
}

.high {
  background-color: #ff6666;
}

.hover-state:hover .hover-state-content {
  display: block;
}
</style>
