<script setup>
import { ref, computed } from "vue";

const props = defineProps({
  task: Object,
});

const emit = defineEmits(["edit-task"]);

// Local state
const isEditing = ref(false);
const editText = ref(props.task.text);

// Computed property to determine priority class for styling
const priorityClass = computed(() => {
  return {
    "priority-high": props.task.priority === "High",
    "priority-medium": props.task.priority === "Medium",
    "priority-low": props.task.priority === "Low",
  };
});

// Method to start editing mode
const startEditing = () => {
  isEditing.value = true;
  editText.value = props.task.text; // Pre-fill with current text
};

// Method to submit the edit
const submitEdit = () => {
  if (editText.value.trim() !== "") {
    emit("edit-task", editText.value);
    isEditing.value = false; // Exit editing mode
  }
};

// Method to cancel editing
const cancelEdit = () => {
  isEditing.value = false; // Exit editing mode without saving
};

// Method to format due date
const formatDate = (date) => {
  if (!date) return "No due date";
  const options = { year: "numeric", month: "long", day: "numeric" };
  return new Date(date).toLocaleDateString(undefined, options);
};
</script>

<template>
  <li class="todo-item">
    <div class="todo-content">
      <!-- Checkbox to toggle task completion -->
      <input
        type="checkbox"
        v-model="task.completed"
        @change="$emit('toggle-complete', task.id)" />
      <!-- Task text -->
      <span :class="{ completed: task.completed }">{{ task.text }}</span>
    </div>

    <!-- Display description -->
    <div>
      <span :class="{ completed: task.completed }">{{ task.description }}</span>
    </div>

    <!-- Display priority and due date -->
    <div class="todo-meta">
      <span class="todo-priority" :class="priorityClass">{{
        task.priority
      }}</span>
      <span class="todo-date">{{ formatDate(task.dueDate) }}</span>
    </div>

    <!-- Action buttons for editing and deleting tasks -->
    <div class="todo-actions">
      <button @click="startEditing">Edit</button>
      <button @click="$emit('delete-task')">Delete</button>
    </div>

    <!-- Inline editing form -->
    <div v-if="isEditing" class="edit-form">
      <input v-model="editText" />
      <button @click="submitEdit">Save</button>
      <button @click="cancelEdit">Cancel</button>
    </div>
  </li>
</template>

<style scoped>
.todo-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: #f9f9f9;
}

.todo-content {
  flex: 1;
  display: flex;
  align-items: center;
}

.todo-content input {
  margin-right: 10px;
}

.todo-content span {
  flex: 1;
}

.todo-content span.completed {
  text-decoration: line-through;
  color: #aaa;
}

.todo-meta {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  margin-right: 10px;
}

.todo-priority {
  font-weight: bold;
}

.priority-high {
  color: #ff4d4d;
}

.priority-medium {
  color: #ffcc00;
}

.priority-low {
  color: #4dff4d;
}

.todo-date {
  font-style: italic;
  color: #555;
}

.todo-actions button {
  margin-left: 5px;
  padding: 5px 10px;
  font-size: 14px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.todo-actions button:hover {
  background-color: #e0e0e0;
}

.edit-form {
  display: flex;
  align-items: center;
  margin-top: 10px;
}

.edit-form input {
  flex: 1;
  padding: 5px;
  margin-right: 10px;
  font-size: 14px;
}

.edit-form button {
  padding: 5px 10px;
  font-size: 14px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.edit-form button:hover {
  background-color: #e0e0e0;
}
</style>
