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
        <Icon
          :class="['priority-indicator', priorityClass]"
          @click="toggleOpen"
          name="material-symbols:circle" />
      </div>
    </div>

    <div v-if="isOpen" class="task-details">
      <div class="todo-description-container">
        <span>{{ task.description || "No description" }}</span>
      </div>
      <div class="details-due-priority">
        <div class="todo-due-date-container">
          <label>Due Date: </label>
          <span>{{ formatDate(task.dueDate) }}</span>
        </div>
        <div class="todo-priority-container" :class="priorityClass">
          <label>Priority: </label>
          <span>{{ task.priority }}</span>
        </div>
      </div>
      <div class="edit-delete-buttons-container">
        <button @click="startEditing" class="save-edit-button">Edit</button>
        <button @click="confirmDelete" class="delete-button">Delete</button>
      </div>
    </div>

    <div v-if="isEditing" class="edit-form">
      <div class="edit-form-container">
        <label for="editTask">Edit Task Name</label>
        <input v-model="editText" placeholder="Edit task" class="edit-input" />
      </div>
      <div class="edit-form-container">
        <label for="editDescription">Edit Description</label>
        <input
          v-model="editDescription"
          placeholder="Edit description"
          class="edit-input" />
      </div>
      <div class="due-pri-container">
        <div class="edit-form-container">
          <label for="editDueDate">Edit Due Date</label>
          <input
            v-model="editDueDate"
            :min="today"
            type="date"
            class="edit-date-input" />
        </div>
        <div class="edit-form-container">
          <label for="editPriority">Edit Priority</label>
          <select v-model="editPriority" class="edit-priority-input">
            <option value="Low">Low</option>
            <option value="Medium">Medium</option>
            <option value="High">High</option>
          </select>
        </div>
        <button @click="submitEdit" class="save-edit-button">Save</button>
        <button @click="cancelEdit" class="no-button">Cancel</button>
      </div>
    </div>

    <!-- er du sikker på du vil slette opgaven modal -->
    <div v-if="showDeleteConfirmation" class="modal-overlay">
      <div class="modal">
        <p>Are you sure you want to delete this task?</p>
        <button @click="deleteTask" class="confirm-button">Yes</button>
        <button @click="cancelDelete" class="no-button">No</button>
      </div>
    </div>
  </li>
</template>

<script setup>
import { ref, computed } from "vue";

const props = defineProps({
  task: Object,
});

const emit = defineEmits(["edit-task", "delete-task"]);

const isEditing = ref(false);
const editText = ref(props.task.text);
const editDescription = ref(props.task.description);
const editDueDate = ref(props.task.dueDate);
const editPriority = ref(props.task.priority);
const isOpen = ref(false);
const showDeleteConfirmation = ref(false);

// find dagens dato
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
  if (!date) return "None";
  const options = { year: "numeric", month: "long", day: "numeric" };
  return new Date(date).toLocaleDateString(undefined, options);
};

const toggleIcon = computed(() => {
  return isOpen.value ? "ep:close-bold" : "ep:arrow-down-bold";
});

const toggleOpen = () => {
  if (isOpen.value) {
    // luk formen Edit og slet ændringer hvis opgaven lukkes
    isEditing.value = false;
  }
  isOpen.value = !isOpen.value;
};

// er du sikker på du vil slette opgaven modal
const confirmDelete = () => {
  showDeleteConfirmation.value = true;
};
const deleteTask = () => {
  emit("delete-task", props.task.id);
  showDeleteConfirmation.value = false;
};
const cancelDelete = () => {
  showDeleteConfirmation.value = false;
};
</script>

<style scoped>
.todo-item {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 10px;
  padding: 10px;
  border: 1px solid #ffb3d9;
  border-radius: 4px;
  background-color: #fff;
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

.todo-content input:checked {
  width: 1.2em;
  height: 1.2rem;
  accent-color: #e04794;
}

.task-details {
  margin-top: 10px;
  width: 100%;
}

.task-details label {
  color: #000;
}

.todo-description-container {
  margin-bottom: 10px;
  padding-bottom: 10px;
  display: grid;
  align-items: flex-start;
  border-bottom: 1px solid #ffb3d9;
  max-width: 100%;
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

.details-due-priority {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  margin-top: 10px;
  margin-bottom: 10px;
}

.edit-delete-buttons-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 5px;
  margin-top: 20px;
  margin-bottom: 10px;
}

.save-edit-button {
  padding: 8px 15px;
  font-size: 14px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
  background-color: #ff99cc;
  color: #fff;
  font-weight: bold;
}

.delete-button {
  margin-left: 5px;
  padding: 8px 15px;
  font-size: 14px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
  color: #ff4d4d;
  font-weight: bold;
}

.delete-button:hover {
  background-color: #ff4d4d;
  color: #fff;
}

.save-edit-button:hover {
  background-color: #ff66b2;
}

.edit-form {
  display: grid;
  grid-template-columns: 1fr;
  gap: 10px;
  margin-top: 10px;
  width: 100%;
}

.edit-form-container {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.due-pri-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
}

.edit-form input {
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ffb3d9;
  border-radius: 4px;
  outline: none;
  transition: border-color 0.3s;
}

.edit-form:focus {
  border-color: #ff66b2;
}

.edit-priority-input {
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ffb3d9;
  border-radius: 4px;
  outline: none;
  transition: border-color 0.3s;
}

.edit-form button {
  padding: 5px 10px;
  font-size: 14px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.edit-form label {
  padding-bottom: 5px;
}

.edit-form input:hover {
  border-color: #ff66b2;
}

.priority-indicator {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  display: inline-block;
  margin-left: 10px;
}
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal {
  background-color: #fff;
  padding: 20px;
  border-radius: 4px;
  text-align: center;
}

.modal p {
  margin-bottom: 20px;
  font-size: 16px;
  font-weight: bold;
}

.confirm-button {
  padding: 8px 15px;
  font-size: 14px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
  background-color: #f0f0f0;
  color: #ff4d4d;
  font-weight: bold;
  margin-right: 10px;
}

.confirm-button:hover {
  background-color: #ff4d4d;
  color: #fff;
}

.no-button {
  padding: 8px 15px;
  font-size: 14px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
  background-color: #f0f0f0;
  color: #000;
  font-weight: bold;
}

.no-button:hover {
  background-color: #ccc;
}
</style>
