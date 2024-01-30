<script>
  import { createEventDispatcher, onMount, tick } from "svelte";
  import TodoList from "./lib/TodoList.svelte";
  import { v4 as uuid } from "uuid";

  let todoList;
  let todos = null;
  let error = null;
  let isLoading = false;
  let isAdding = false;
  let disabledItems = [];

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

    isAdding = true;

    const response = await fetch("https://jsonplaceholder.typicode.com/todos", {
      method: "POST",
      body: JSON.stringify({
        title: event.detail.title,
        completed: false,
      }),
      headers: {
        "Content-type": "application/json; charset=UTF-8",
      },
    });

    if (response.ok) {
      const todo = await response.json();
      todos = [...todos, { ...todo, id: uuid() }];

      todoList.clearInput();
    } else {
      alert("An error has occurred.");
    }

    isAdding = false;

    await tick();

    todoList.focusInput();
  }

  async function handleRemoveTodo(event) {
    const id = event.detail.id;

    if (disabledItems.includes(id)) return;

    // Disable the item while it's being removed to avoid race conditions
    disabledItems = [...disabledItems, id];

    const response = await fetch(
      `https://jsonplaceholder.typicode.com/todos/${id}`,
      {
        method: "DELETE",
      }
    );

    if (response.ok) {
      todos = todos.filter((todo) => todo.id !== event.detail.id);
    } else {
      alert("An error has occurred.");
    }

    disabledItems = disabledItems.filter((itemId) => itemId !== id);
  }

  async function handleToggleTodo(event) {
    const id = event.detail.id;

    if (disabledItems.includes(id)) return;

    // Disable the item while it's being removed to avoid race conditions
    disabledItems = [...disabledItems, id];

    const response = await fetch(
      `https://jsonplaceholder.typicode.com/todos/${id}`,
      {
        method: "PATCH",
        body: JSON.stringify({
          completed: event.detail.value,
        }),
        headers: {
          "Content-type": "application/json; charset=UTF-8",
        },
      }
    );

    // Optimistic update
    if (response.ok) {
      todos = todos.map((todo) =>
        todo.id === event.detail.id
          ? { ...todo, completed: event.detail.value }
          : todo
      );
    } else {
      alert("An error has occurred.");
    }

    disabledItems = disabledItems.filter((itemId) => itemId !== id);
  }
</script>

<TodoList
  {todos}
  {error}
  {isLoading}
  {disabledItems}
  disableAdding={isAdding}
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
