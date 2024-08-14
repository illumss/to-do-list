<template>
  <div id="app">
    <h1>Todo List</h1>
    <todo-list />
  </div>
</template>

<script setup>
import TodoList from "./components/TodoList.vue";
</script>

<style scoped>
#app {
  max-width: 600px;
  margin: 50px auto;
  padding: 20px;
  background-color: #ffffff;
  border-radius: 10px;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
  text-align: center;
}

h1 {
  margin-bottom: 20px;
  font-size: 2.5rem;
  color: #333;
}
</style>
