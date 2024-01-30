<svelte:options immutable={true} />

<script>
  import Button from "./Button.svelte";
  import { afterUpdate, createEventDispatcher } from "svelte";
  import FaRegTrashAlt from "svelte-icons/fa/FaRegTrashAlt.svelte";

  let input, listDiv, autoscroll, listDivScrollHeight;
  let inputText = "";

  export let todos = [];
  let prevTodos = todos;

  afterUpdate(() => {
    if (autoscroll) {
      listDiv.scrollTop = listDiv.scrollHeight;

      autoscroll = false;
    }
  });

  $: {
    autoscroll = todos.length > prevTodos.length;
    prevTodos = todos;
  }

  export function clearInput() {
    inputText = "";
  }
  export function focusInput() {
    input.focus();
  }

  const dispatch = createEventDispatcher();

  function handleAddTodo() {
    const isNotCancelled = dispatch(
      "addTodo",
      {
        title: inputText,
      },
      { cancelable: true }
    );

    if (isNotCancelled) {
      inputText = "";
    }
  }

  function handleRemoveTodo(id) {
    dispatch("removeTodo", { id });
  }

  function handleToggleTodo(id, value) {
    dispatch("toggleTodo", { id, value });
  }
</script>

<div class="todo-list-wrapper">
  <div class="todo-list" bind:this={listDiv}>
    <div bind:offsetHeight={listDivScrollHeight}>
      {#if todos.length === 0}
        <p>No todos</p>
      {:else}
        <ul>
          {#each todos as { id, title, completed } (id)}
            <!-- {(console.log("todo", id, title, completed), "")} -->
            <!-- {@debug id, title, completed} -->
            <li>
              <label for="todo">
                <input
                  on:input={(event) => {
                    // Ensure that the checkbox is always in sync with the 'completed' value from the state.
                    event.currentTarget.checked = completed;

                    // We use the inverse of 'completed' when we call the 'handleToggleTodo' function. 'completed' is the existing value from the state before the user has interacted with the checkbox.
                    handleToggleTodo(id, !completed);
                  }}
                  type="checkbox"
                  checked={completed}
                />
                {title}
              </label>
              <button
                aria-label="Remove Todo"
                on:click={() => handleRemoveTodo(id)}
                ><span style:width="10px" style:display="inline-block"
                  ><FaRegTrashAlt /></span
                ></button
              >
            </li>
          {/each}
        </ul>
      {/if}
    </div>
  </div>
  <form class="add-todo-form" on:submit|preventDefault={handleAddTodo}>
    <input bind:this={input} bind:value={inputText} placeholder="New Todo" />
    <Button aria-label="Add Todo" disabled={!inputText} type="submit"
      >Add Todo</Button
    >
  </form>
</div>

<style>
  .todo-list {
    max-height: 150px;
    overflow: auto;
  }
</style>
