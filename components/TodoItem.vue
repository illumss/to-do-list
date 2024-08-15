<script setup>
import { ref, computed } from "vue";

const props = defineProps({
  task: Object,
});

const emit = defineEmits(["edit-task"]);

const isEditing = ref(false);
const editText = ref(props.task.text);
const editDescription = ref(props.task.description);
const editDueDate = ref(props.task.dueDate);
const editPriority = ref(props.task.priority);
const isOpen = ref(false);

// Calculate today's date in YYYY-MM-DD format
const today = new Date().toISOString().split("T")[0];

const priorityClass = computed(() => {
  return {
    "priority-high": props.task.priority === "High",
    "priority-medium": props.task.priority === "Medium",
    "priority-low": props.task.priority === "Low",
  };
});

const startEditing = () => {
  isEditing.value = true;
  editText.value = props.task.text;
  editDescription.value = props.task.description;
  editDueDate.value = props.task.dueDate;
  editPriority.value = props.task.priority;
};

const capitalizeFirstLetter = (string) => {
  return string.charAt(0).toUpperCase() + string.slice(1);
};

const submitEdit = () => {
  if (editText.value.trim() !== "") {
    emit("edit-task", {
      text: capitalizeFirstLetter(editText.value.trim()),
      description: capitalizeFirstLetter(editDescription.value.trim()),
      dueDate: editDueDate.value,
      priority: editPriority.value,
    });
    isEditing.value = false;
  }
};

const cancelEdit = () => {
  isEditing.value = false;
};

const formatDate = (date) => {
  if (!date) return "No due date";
  const options = { year: "numeric", month: "long", day: "numeric" };
  return new Date(date).toLocaleDateString(undefined, options);
};

// Compute the icon name based on the isOpen state
const toggleIcon = computed(() => {
  return isOpen.value ? "ep:close-bold" : "ep:arrow-down-bold";
});

const toggleOpen = () => {
  isOpen.value = !isOpen.value;
};
</script>

<template>
  <li class="todo-item">
    <div class="todo-content">
      <input
        type="checkbox"
        :checked="task.completed"
        @change="$emit('toggle-complete', task.id)" />
      <span :class="{ completed: task.completed }" class="task-name">
        {{ task.text }}
      </span>
      <div>
        <Icon @click="toggleOpen" :name="toggleIcon" class="arrow-icon" />
        <!-- Priority indicator is always visible -->

        <Icon
          :class="['priority-indicator', priorityClass]"
          @click="toggleOpen"
          name="material-symbols:circle" />
      </div>
    </div>

    <div v-if="isOpen" class="task-details">
      <div class="todo-description-container">
        <label>Description:</label>
        <span>{{ task.description || "No description" }}</span>
      </div>
      <div class="todo-due-date-container">
        <label>Due Date:</label>
        <span>{{ formatDate(task.dueDate) }}</span>
      </div>
      <div class="todo-priority-container" :class="priorityClass">
        <label>Priority:</label>
        <span>{{ task.priority }}</span>
      </div>

      <button @click="startEditing" class="edit-button">Edit</button>
      <button @click="$emit('delete-task', task.id)" class="delete-button">
        Delete
      </button>
    </div>

    <div v-if="isEditing" class="edit-form">
      <input v-model="editText" placeholder="Edit task" class="edit-input" />
      <input
        v-model="editDescription"
        placeholder="Edit description"
        class="edit-input" />
      <input
        v-model="editDueDate"
        :min="today"
        type="date"
        class="edit-date-input" />
      <select v-model="editPriority" class="edit-priority-input">
        <option value="Low">Low</option>
        <option value="Medium">Medium</option>
        <option value="High">High</option>
      </select>
      <button @click="submitEdit" class="save-button">Save</button>
      <button @click="cancelEdit" class="cancel-button">Cancel</button>
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

.edit-button {
  padding: 5px 10px;
  font-size: 14px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.delete-button {
  margin-left: 5px;
  padding: 5px 10px;
  font-size: 14px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
  color: red;
}

.save-button {
  margin-left: 5px;
  padding: 5px 10px;
  font-size: 14px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.cancel-button {
  margin-left: 5px;
  padding: 5px 10px;
  font-size: 14px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
  color: red;
}

.todo-actions button:hover {
  background-color: #e0e0e0;
}

.task-details button:hover {
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

.priority-indicator {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  display: inline-block;
  margin-left: 10px;
}
</style>
