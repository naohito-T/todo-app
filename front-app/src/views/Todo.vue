<template>
  <div class="todo">
    <h1>TODO APP</h1>
    <div class="create">
      <div class="title">
        <label for="todo_create">Title:</label>
        <input id="todo_create" type="text" v-model="state.title">
        <button @click="createTodo">Create</button>
      </div>
    </div>
    <div v-for="todo in state.todos" :key="todo.uuid">
      <!-- <div class="title margin-r1">Title: {{ todo.title }}</div> -->
      <input type="text" class="margin-r1" v-model="todo.title">
      <!-- <div class="status margin-r1">Status: {{ todo.status }}</div> -->
      <select class="margin-r1" v-model="todo.status">
        <option value="todo">TODO</option>
        <option value="wip">WIP</option>
        <option value="down">DONE</option>
      </select>
      <button class="update margin-r1" @click="updateTodo(todo)">Update</button>
      <button class="delete margin-r1" @click="deleteTodo(todo)">Delete</button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive } from "vue";
import axios from 'axios';

const baseURL = 'http://localhost:3000/';

type Todo = {
  uuid: string;
  title: string;
  status: 'todo' | 'wip' | 'done';
};

export default defineComponent({
  name: "Todo",
  setup() {
    const state = reactive({
      title: '',
      todos: [],
    });
    // Todo select
    const getTodos = () => {
      axios.get(baseURL).then((res) => {
        if (res && res.data) {
          console.log(res);
          state.todos = res.data.resBody;
        }
      });
    };
    getTodos();

    // Todo create
    const createTodo = async () => {
      await axios.put(baseURL, { title: state.title });
      getTodos();
    };

    // Todo update
    const updateTodo = async (todo: Todo) => {
      await axios.post(baseURL + todo.uuid, {
        title: todo.title,
        status: todo.status,
      });
      getTodos();
    };

    // Todo Delete
    const deleteTodo = async (todo: Todo) => {
      await axios.delete(baseURL + todo.uuid);
      getTodos();
    };

    return {
      state,
      createTodo,
      updateTodo,
      deleteTodo,
    };
  },
});
</script>

<style lang="scss" scoped>
.todo-item {
  display: flex;
  justify-content: center;

  .margin-r1 {
    margin-right: 1rem;
  }
}
</style>
