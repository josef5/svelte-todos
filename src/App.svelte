<script>
  import { createEventDispatcher, onMount } from "svelte";
  import TodoList from "./lib/TodoList.svelte";
  import { v4 as uuid } from "uuid";

  let todoList;
  let todos = null;
  let error = null;
  let isLoading = false;

  onMount(() => {
    loadTodos();
  });

  async function loadTodos() {
    isLoading = true;

    await fetch("https://jsonplaceholder.typicode.com/todos?_limit=10").then(
      async (response) => {
        if (response.ok) {
          todos = await response.json();
        } else {
          error = "An error has occurred";
        }
      }
    );

    isLoading = false;
  }

  $: console.log("todos", todos);

  function handleAddTodo(event) {
    event.preventDefault();

    todos = [
      ...todos,
      {
        id: uuid(),
        title: event.detail.title,
        completed: false,
      },
    ];

    todoList.clearInput();
  }

  function handleRemoveTodo(event) {
    todos = todos.filter((todo) => todo.id !== event.detail.id);
  }

  function handleToggleTodo(event) {
    todos = todos.map((todo) =>
      todo.id === event.detail.id
        ? { ...todo, completed: event.detail.value }
        : todo
    );
  }
</script>

<TodoList
  {todos}
  {error}
  {isLoading}
  bind:this={todoList}
  on:addTodo={handleAddTodo}
  on:removeTodo={handleRemoveTodo}
  on:toggleTodo={handleToggleTodo}
/>

<style>
  @import url("https://fonts.googleapis.com/css2?family=Roboto&display=swap");

  :global(body) {
    font-family: "Roboto", sans-serif;
  }
</style>
