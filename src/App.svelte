<script>
  import { createEventDispatcher } from "svelte";
  import TodoList from "./lib/TodoList.svelte";
  import { v4 as uuid } from "uuid";

  let todoList;

  let todos = [
    { id: uuid(), title: "Todo 1", completed: true },
    { id: uuid(), title: "Todo 2", completed: false },
    { id: uuid(), title: "Todo 3", completed: true },
  ];

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
