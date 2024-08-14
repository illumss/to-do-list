<template>
  <div>
    <TodoInput @add-task="addTask" />

    <!-- Sort Dropdown -->
    <div class="sort-container">
      <label for="sort">Sort by:</label>
      <select id="sort" v-model="sortOrder" @change="sortTasks">
        <option value="alphabetical">Alphabetical</option>
        <option value="created">Creation Date</option>
      </select>
    </div>

    <!-- Active Tasks Section -->
    <h2>Active Tasks</h2>
    <ul v-if="sortedActiveTasks.length">
      <TodoItem
        v-for="task in sortedActiveTasks"
        :key="task.id"
        :task="task"
        @delete-task="deleteTask(task.id)"
        @edit-task="editTask(task.id, $event)"
        @toggle-complete="toggleComplete(task.id)" />
    </ul>
    <p v-else>No active tasks</p>

    <!-- Completed Tasks Section -->
    <h2>Completed Tasks</h2>
    <ul v-if="completedTasks.length">
      <TodoItem
        v-for="task in completedTasks"
        :key="task.id"
        :task="task"
        @delete-task="deleteTask(task.id)"
        @edit-task="editTask(task.id, $event)"
        @toggle-complete="toggleComplete(task.id)" />
    </ul>
    <p v-else>No completed tasks</p>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import TodoItem from "~/components/TodoItem.vue";
import TodoInput from "~/components/TodoInput.vue";

const tasks = ref([]);

// Sort Order
const sortOrder = ref("created");

// Computed properties for active and completed tasks
const activeTasks = computed(() =>
  tasks.value.filter((task) => !task.completed)
);
const completedTasks = computed(() =>
  tasks.value.filter((task) => task.completed)
);

const sortedActiveTasks = computed(() => {
  switch (sortOrder.value) {
    case "alphabetical":
      return [...activeTasks.value].sort((a, b) =>
        a.text.localeCompare(b.text)
      );
    case "created":
    default:
      return activeTasks.value;
  }
});

const addTask = (task) => {
  tasks.value.push({ id: Date.now(), text: task, completed: false });
  saveTasks();
};

const deleteTask = (taskId) => {
  tasks.value = tasks.value.filter((task) => task.id !== taskId);
  saveTasks();
};

const editTask = (taskId, newTask) => {
  const task = tasks.value.find((task) => task.id === taskId);
  if (task) task.text = newTask;
  saveTasks();
};

const toggleComplete = (taskId) => {
  const task = tasks.value.find((task) => task.id === taskId);
  if (task) task.completed = !task.completed;
  saveTasks();
};

const sortTasks = () => {
  sortedActiveTasks.value; // Triggers reactivity
};

const saveTasks = () => {
  localStorage.setItem("tasks", JSON.stringify(tasks.value));
};

onMounted(() => {
  const savedTasks = JSON.parse(localStorage.getItem("tasks"));
  if (savedTasks) {
    tasks.value = savedTasks;
  }
});
</script>

<style scoped>
/* Style the sort container */
.sort-container {
  margin-bottom: 10px;
  display: flex;
  align-items: center;
}

.sort-container label {
  margin-right: 10px;
}

.sort-container select {
  padding: 5px;
  font-size: 16px;
  border-radius: 4px;
  border: 1px solid #ccc;
}

h2 {
  margin-top: 20px;
  font-size: 1.5em;
  color: #333;
}

ul {
  list-style-type: none;
  padding: 0;
}

p {
  font-style: italic;
  color: #777;
}
</style>
