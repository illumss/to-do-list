<script>
import { defineNuxtComponent } from "#app";

export default defineNuxtComponent({
  data: () => ({
    todoList: [],
  }),
  computed: {
    completedItems() {
      return this.todoList.filter((item) => item.completed);
    },
    remainingItems() {
      return this.todoList.filter((item) => !item.completed);
    },
  },
  methods: {
    fetchTodoList() {
      fetch("https://jsonplaceholder.typicode.com/todos/")
        .then((response) => response.json())
        .then((json) => {
          this.todoList = json;
        });
    },
  },
});
</script>

<template>
  <div class="container">
    <img src="/todo.jpg" alt="todo photo" />

    <h1 class="intro">To-do list!</h1>
    <button @click="fetchTodoList">Fetch</button>
    <p>
      {{ completedItems.length }} completed |
      {{ remainingItems.length }} remaining
    </p>
    <ul>
      <li v-for="todo in todoList" :key="`todo-id-${todo.id}`">
        <input type="checkbox" :checked="todo.completed" /> {{ todo.title }}
      </li>
    </ul>
  </div>
</template>

<style scoped>
.container {
  max-width: 800px;
  margin: 0 auto;
}
h1 {
  font-size: 50px;
  color: #333;
  text-align: center;
  padding-top: 20px;
}
</style>
