<template>
  <li class="todo-item">
    <input
      type="checkbox"
      :checked="task.completed"
      @change="emitToggleComplete"
      class="checkbox" />
    <span :class="{ completed: task.completed }" class="task-text">{{
      task.text
    }}</span>
    <button @click="toggleEditMode" class="edit-btn">
      {{ editMode ? "Save" : "Edit" }}
    </button>
    <button @click="emitDeleteTask" class="delete-btn">Delete</button>
    <input
      v-if="editMode"
      v-model="newTask"
      @keyup.enter="saveEdit"
      class="edit-input" />
  </li>
</template>

<script setup>
import { ref } from "vue";

const props = defineProps({
  task: Object,
});

const emit = defineEmits(["delete-task", "edit-task", "toggle-complete"]);

const editMode = ref(false);
const newTask = ref(props.task.text);

const emitToggleComplete = () => {
  emit("toggle-complete");
};

const emitDeleteTask = () => {
  emit("delete-task");
};

const toggleEditMode = () => {
  editMode.value = !editMode.value;
  if (!editMode.value) {
    saveEdit();
  }
};

const saveEdit = () => {
  emit("edit-task", newTask.value);
};
</script>

<style scoped>
.todo-item {
  display: flex;
  align-items: center; /* Center vertically */
  justify-content: space-between; /* Space between items */
  padding: 10px 0;
  border-bottom: 1px solid #ddd;
  list-style-type: none; /* Remove default list styling */
}

.checkbox {
  margin-right: 10px; /* Space between checkbox and text */
}

.task-text {
  flex: 1; /* Allow text to take up remaining space */
  text-align: left;
  margin-right: 10px;
  font-size: 16px;
}

.completed {
  text-decoration: line-through;
  color: #999;
}

.edit-btn,
.delete-btn {
  background-color: transparent;
  border: none;
  cursor: pointer;
  color: #007bff;
  margin-left: 5px;
}

.edit-btn:hover,
.delete-btn:hover {
  color: #0056b3;
}

.edit-input {
  flex: 1;
  margin-left: 10px;
}
</style>
