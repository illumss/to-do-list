<script setup>
import { ref, computed, onMounted } from "vue";
import TodoItem from "~/components/TodoItem.vue";
import TodoInput from "~/components/TodoInput.vue";
import TodoItemDetails from "~/components/TodoItemDetails.vue";

const tasks = ref([]);
const sortOrder = ref("created");

const activeTasks = computed(() =>
  tasks.value.filter((task) => !task.completed)
);
const completedTasks = computed(() =>
  tasks.value.filter((task) => task.completed)
);

const sortedActiveTasks = computed(() => {
  switch (sortOrder.value) {
    case "dueDate":
      return [...activeTasks.value].sort(
        (a, b) => new Date(a.dueDate) - new Date(b.dueDate)
      );
    case "priority":
      const priorityMap = { High: 1, Medium: 2, Low: 3 };
      return [...activeTasks.value].sort(
        (a, b) => priorityMap[a.priority] - priorityMap[b.priority]
      );
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
  console.log("Adding task:", task);
  tasks.value.push({ id: Date.now(), ...task });
  saveTasks();
};

const deleteTask = (taskId) => {
  console.log("Deleting task with ID:", taskId);
  tasks.value = tasks.value.filter((task) => task.id !== taskId);
  saveTasks();
};

const editTask = (taskId, newTask) => {
  console.log("Editing task with ID:", taskId, "New text:", newTask);
  const task = tasks.value.find((task) => task.id === taskId);
  if (task) {
    task.text = newTask;
    saveTasks();
  }
};

const toggleComplete = (taskId) => {
  console.log("Toggling completion for task ID:", taskId);
  const task = tasks.value.find((task) => task.id === taskId);
  if (task) {
    task.completed = !task.completed;
    console.log("Updated task:", task);
    saveTasks();
  }
};

const sortTasks = () => {
  console.log("Sorting tasks by:", sortOrder.value);
  sortedActiveTasks.value;
};

const saveTasks = () => {
  console.log("Saving tasks to localStorage:", tasks.value);
  localStorage.setItem("tasks", JSON.stringify(tasks.value));
};

const loadTasks = () => {
  const savedTasks = JSON.parse(localStorage.getItem("tasks"));
  console.log("Loading tasks from localStorage:", savedTasks);
  if (savedTasks) {
    tasks.value = savedTasks.map((task) => ({
      ...task,
      completed: task.completed ?? false,
    }));
    console.log("Tasks after loading:", tasks.value);
  }
};

onMounted(() => {
  loadTasks();
});
</script>

<template>
  <div>
    <TodoInput @add-task="addTask" />

    <!-- Sort Dropdown -->
    <div class="sort-container">
      <label for="sort">Sort by:</label>
      <select id="sort" v-model="sortOrder" @change="sortTasks">
        <option value="dueDate">Due Date</option>
        <option value="priority">Priority</option>
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

<style scoped>
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
