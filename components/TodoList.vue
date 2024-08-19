<template>
  <!--  yderste lag af to do listen -->

  <div>
    <TodoInput @add-task="addTask" />

    <!--  sorterings-knap -->
    <div class="sort-container">
      <label for="sort">Sort by:</label>
      <select id="sort" v-model="sortOrder" @change="sortTasks">
        <option value="dueDate">Due Date</option>
        <option value="priority">Priority</option>
        <option value="alphabetical">Alphabetical</option>
        <option value="created">Creation Date</option>
      </select>
    </div>

    <!--  liste over aktive opgaver -->
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
    <!-- hvis der ikke er nogen aktive opgaver -->
    <p v-else>No active tasks</p>

    <!--  liste over færdige opgaver -->
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
    <!-- hvis der ikke er nogen færdige opgaver -->
    <p v-else>No completed tasks</p>
  </div>
</template>

<script setup>
const tasks = ref([]);
const sortOrder = ref("created"); //  sorteringsrækkefølge

const activeTasks = computed(
  () => tasks.value.filter((task) => !task.completed) //  aktive opgaver
);
const completedTasks = computed(
  () => tasks.value.filter((task) => task.completed) //  færdige opgaver
);

//  sorteringsfunktion til at sortere opgaverne
const sortedActiveTasks = computed(() => {
  switch (sortOrder.value) {
    case "dueDate":
      return [...activeTasks.value].sort(
        (a, b) => new Date(a.dueDate) - new Date(b.dueDate) //  sorterer efter due date
      );
    case "priority":
      const priorityMap = { High: 1, Medium: 2, Low: 3 };
      return [...activeTasks.value].sort(
        (a, b) => priorityMap[a.priority] - priorityMap[b.priority] //  sorterer efter prioritet
      );
    case "alphabetical":
      return [...activeTasks.value].sort(
        (a, b) => a.text.localeCompare(b.text) //  sorterer alfabetisk
      );
    case "created":
    default:
      return activeTasks.value; //  sorterer efter oprettelsesdato
  }
});

const addTask = (task) => {
  tasks.value.push({ id: Date.now(), ...task });
  saveTasks(); //  gemmer opgaverne i local storage
};

const deleteTask = (taskId) => {
  tasks.value = tasks.value.filter((task) => task.id !== taskId);
  saveTasks(); //  sletter opgaverne i local storage
};

// redigerer opgaverne
const editTask = (taskId, newTask) => {
  const task = tasks.value.find((task) => task.id === taskId);
  if (task) {
    task.text = newTask.text;
    task.description = newTask.description;
    task.dueDate = newTask.dueDate;
    task.priority = newTask.priority;
    saveTasks();
  }
};

const toggleComplete = (taskId) => {
  const task = tasks.value.find((task) => task.id === taskId);
  if (task) {
    task.completed = !task.completed;
    saveTasks(); //  gemmer færdige opgaver i local storage
  }
};

const sortTasks = () => {
  sortedActiveTasks.value;
};

const saveTasks = () => {
  localStorage.setItem("tasks", JSON.stringify(tasks.value));
};

//  henter opgaverne fra local storage
const loadTasks = () => {
  const savedTasks = JSON.parse(localStorage.getItem("tasks"));
  if (savedTasks) {
    tasks.value = savedTasks.map((task) => ({
      ...task,
      completed: task.completed ?? false,
    }));
  }
};

onMounted(() => {
  loadTasks();
});
</script>

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
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ffb3d9;
  border-radius: 4px;
  outline: none;
  transition: border-color 0.3s;
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
