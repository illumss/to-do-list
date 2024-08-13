<script></script>

<template>
  <div class="List">
    <ul>
      <li class="list">
        <input type="checkbox" id="task1" name="task1" value="task1" />
        <label for="task1">Task 1: Wake up</label>
      </li>
      <li class="list">
        <input type="checkbox" id="task2" name="task2" value="task2" />
        <label for="task2">Task 2: Get dressed</label>
      </li>
      <li class="list">
        <input type="checkbox" id="task3" name="task3" value="task3" />
        <label for="task3">Task 3: Drink coffee</label>
      </li>
      <li class="list">
        <input type="checkbox" id="task4" name="task4" value="task4" />
        <label for="task4">Task 4: Code</label>
      </li>
    </ul>

    <div>
      <input type="text" placeholder="Add task here..." />
      <button class="button" id="add">Add task</button>
    </div>
    <div class="bottom-buttons">
      <button class="button" id="edit">Edit task</button>
      <button class="button" id="delete">Delete task</button>
      <button class="button" id="sort">Sort tasks</button>
    </div>
  </div>
</template>

<style scoped>
.list {
  font-size: 30px;
  color: #333;
  padding-top: 20px;
  list-style-type: none;
}

input[type="checkbox"] {
  width: 30px;
  height: 30px;
  margin-right: 10px;
}

input[type="text"] {
  width: 300px;
  height: 30px;
  margin-right: 10px;
}

.bottom-buttons {
  padding-top: 20px;
  display: flex;
}

.button {
  width: 100px;
  height: 30px;
  background-color: white;
  color: black;
  border-color: blue;
  border-radius: 15px;
  cursor: pointer;
  margin-right: 30px;
}

.button:hover {
  background-color: blue;
  color: white;
}
</style>
