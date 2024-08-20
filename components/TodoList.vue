<!--  yderste lag af to do listen -->

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

<template>
  <div>
    <TodoInput @add-task="addTask" />

    <!--  sorterings-knap -->
    <div class="mb-2.5 flex items-center">
      <label for="sort" class="mr-2.5">Sort by:</label>
      <select
        id="sort"
        v-model="sortOrder"
        @change="sortTasks"
        class="p-2.5 text-base outline-none border rounded border-#ffb3d9 transition duration-300 focus:border-#ff66b2">
        <option value="dueDate">Due Date</option>
        <option value="priority">Priority</option>
        <option value="alphabetical">Alphabetical</option>
        <option value="created">Creation Date</option>
      </select>
    </div>

    <!--  liste over aktive opgaver -->
    <h2 class="mt-5 text-2xl text-gray-900">Active Tasks</h2>

    <template v-if="sortedActiveTasks.length">
      <TransitionGroup name="list" tag="ul" class="list-none p-0">
        <TodoItem
          v-for="task in sortedActiveTasks"
          :key="task.id"
          :task="task"
          @delete-task="deleteTask(task.id)"
          @edit-task="editTask(task.id, $event)"
          @toggle-complete="toggleComplete(task.id)"
      /></TransitionGroup>
    </template>
    <!-- hvis der ikke er nogen aktive opgaver -->
    <p v-else class="italic color-#777">No active tasks</p>

    <!--  liste over færdige opgaver -->
    <h2 class="mt-5 text-2xl text-gray-900">Completed Tasks</h2>
    <template v-if="completedTasks.length">
      <TransitionGroup name="list" tag="ul" class="list-none p-0">
        <TodoItem
          v-for="task in completedTasks"
          :key="task.id"
          :task="task"
          @delete-task="deleteTask(task.id)"
          @edit-task="editTask(task.id, $event)"
          @toggle-complete="toggleComplete(task.id)" />
      </TransitionGroup>
    </template>
    <!-- hvis der ikke er nogen færdige opgaver -->
    <p v-else class="italic color-#777">No completed tasks</p>
  </div>
</template>

<style>
.list-move,
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
</style>
