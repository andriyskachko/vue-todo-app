<script setup>
import { onMounted, ref, computed } from 'vue';
const todoText = ref('');
const todos = ref([]);
const currentFilter = ref('all');

const filteredTodos = computed(() => {
  switch (currentFilter.value) {
    case 'all':
      return todos.value;
    case 'active':
      return todos.value.filter((todo) => !todo.done);
    case 'completed':
      return todos.value.filter((todo) => todo.done);
  }
});

const activeTodos = computed(() => {
  return todos.value.filter((todo) => !todo.done);
});

let id = 0;

function createTodo() {
  if (!todoText.value) return;
  todos.value.push({ done: false, text: todoText.value, id: id++ });
  todoText.value = '';
}

function toggleDone(todo) {
  todo.done = !todo.done;
}

function removeTodo(todo) {
  todos.value = todos.value.filter((t) => t !== todo);
}

function removeCompleted() {
  todos.value = todos.value.filter((todo) => !todo.done);
}

function markTodosCompleted() {
  if (todos.value.some((todo) => !todo.done)) {
    todos.value.forEach((todo) => (todo.done = true));
  } else {
    uncheckTodos();
  }
}

function uncheckTodos() {
  todos.value.forEach((todo) => (todo.done = false));
}

function setFilterOption(e) {
  currentFilter.value = e.target.value;
  console.log(currentFilter.value);
}
</script>

<template>
  <header>
    <h1>Todo</h1>
  </header>
  <main>
    <form @submit.prevent="createTodo">
      <label for="new-todo">New Todo</label>
      <input
        type="text"
        id="new-todo"
        placeholder="Wash the dishes..."
        v-model="todoText"
      />
      <button type="submit">Add Todo</button>
    </form>
    <button @click="markTodosCompleted">Check all todos</button>
    <button @click="removeCompleted">Remove completed todos</button>
    <ul>
      <li v-for="todo in filteredTodos" :key="todo.id">
        <span :class="{ done: todo.done }" @click="toggleDone(todo)">{{
          todo.text
        }}</span>
        <button @click="removeTodo(todo)">X</button>
      </li>
    </ul>
    <div>
      <input type="radio" value="all" v-model="currentFilter" /> All
      <input type="radio" value="active" v-model="currentFilter" /> Active
      <input type="radio" value="completed" v-model="currentFilter" /> Completed
    </div>
    <span
      >{{ activeTodos.length }} item{{
        activeTodos.length === 1 ? '' : 's'
      }}
      left</span
    >
  </main>
</template>

<style scoped>
.done {
  text-decoration: line-through;
}
</style>
