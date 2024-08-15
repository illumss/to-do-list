<script setup>
import { ref, computed } from "vue";

const props = defineProps({
  task: Object,
});

const emit = defineEmits(["edit-task"]);

// Local state
const isEditing = ref(false);
const editText = ref(props.task.text);
const isOpen = ref(false);

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

// Method to toggle details visibility
const toggleOpen = () => {
  isOpen.value = !isOpen.value;
};
</script>

<template>
  <li class="todo-item">
    <div class="todo-content">
      <!-- Checkbox to toggle task completion -->
      <input
        type="checkbox"
        :checked="task.completed"
        @change="$emit('toggle-complete', task.id)" />
      <!-- Task text with toggle functionality -->
      <span :class="{ completed: task.completed }" class="task-name">
        {{ task.text }}
      </span>
      <Icon @click="toggleOpen" name="ep:arrow-down-bold" class="arrow-icon" />
    </div>

    <!-- Toggleable task details -->
    <div v-if="isOpen" class="task-details">
      <!-- Display description -->
      <div class="todo-description-container">
        <label>Description:</label>
        <span class="description-look" :class="todo - description">{{
          task.description
        }}</span>
      </div>

      <!-- Display priority and due date -->
      <div class="todo-meta">
        <label>Priority:</label>
        <span class="todo-priority" :class="priorityClass">
          {{ task.priority }}
        </span>
        <label>Due Date:</label>
        <span class="todo-date">{{ formatDate(task.dueDate) }}</span>
      </div>

      <!-- Action buttons for editing and deleting tasks -->
      <div class="todo-actions">
        <Icon
          @click="startEditing"
          class="editButton"
          name="material-symbols:edit-rounded" />
        <Icon
          @click="$emit('delete-task')"
          class="deleteButton"
          name="ep:delete-filled" />
      </div>

      <!-- Inline editing form -->
      <div v-if="isEditing" class="edit-form">
        <input v-model="editText" />

        <Icon
          @click="submitEdit"
          class="SaveButton"
          name="ep:circle-check-filled" />

        <Icon
          @click="cancelEdit"
          class="cancelButton"
          name="ep:circle-close-filled" />
      </div>
    </div>
  </li>
</template>

<style scoped>
.todo-item {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 10px;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: #f9f9f9;
}

.todo-content {
  display: flex;
  align-items: center;
  width: 100%;
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

.task-details {
  margin-top: 10px;
  width: 100%;
}

.todo-description-container {
  margin-bottom: 10px;
  display: grid;
  align-items: flex-start;
}

.description-look {
  padding-bottom: 5px;
}

.arrow-icon {
  cursor: pointer;
  color: #000;
}

.todo-meta {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  align-items: flex-start;
  margin-top: 5px;
  padding-bottom: 5px;
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

.todo-actions {
  margin-top: 10px;
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

.editButton {
  padding: 5px 10px;
  font-size: 14px;
  cursor: pointer;
  color: #555;
}

.deleteButton {
  margin-left: 5px;
  padding: 5px 10px;
  font-size: 14px;
  cursor: pointer;
  color: #555;
}

.SaveButton {
  margin-left: 5px;
  padding: 5px 10px;
  font-size: 14px;
  cursor: pointer;
  color: green;
}

.cancelButton {
  margin-left: 5px;
  padding: 5px 10px;
  font-size: 14px;
  cursor: pointer;
  color: #555;
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
