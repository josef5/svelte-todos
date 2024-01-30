<svelte:options immutable={true} />

<script>
  import Button from "./Button.svelte";
  import { afterUpdate, createEventDispatcher } from "svelte";
  import FaRegTrashAlt from "svelte-icons/fa/FaRegTrashAlt.svelte";

  export let todos = null;
  export let error;
  export let isLoading = false;
  export let disableAdding = false;
  export let disabledItems = [];

  let prevTodos = todos;
  let inputText = "";
  let input, listDiv, autoscroll, listDivScrollHeight;

  $: {
    autoscroll = todos && prevTodos && todos.length > prevTodos.length;
    prevTodos = todos;
  }

  afterUpdate(() => {
    if (autoscroll) {
      listDiv.scrollTop = listDiv.scrollHeight;

      autoscroll = false;
    }
  });

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
  {#if isLoading}
    <p class="state-text">Loading...</p>
  {:else if error}
    <p class="state-text">{error}</p>
  {:else if todos}
    <div class="todo-list" bind:this={listDiv}>
      <div bind:offsetHeight={listDivScrollHeight}>
        {#if todos.length === 0}
          <p class="state-text">No todos</p>
        {:else}
          <ul>
            {#each todos as { id, title, completed } (id)}
              <!-- {(console.log("todo", id, title, completed), "")} -->
              <!-- {@debug id, title, completed} -->
              <li class:completed>
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
                  class="remove-todo-button"
                  aria-label="Remove todo: {title}"
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
  {/if}

  <form class="add-todo-form" on:submit|preventDefault={handleAddTodo}>
    <input
      bind:this={input}
      bind:value={inputText}
      placeholder="New Todo"
      disabled={disableAdding || isLoading}
    />
    <Button
      aria-label="Add Todo"
      disabled={!inputText || disableAdding || isLoading}
      type="submit">Add</Button
    >
  </form>
</div>

<style lang="scss">
  .todo-list-wrapper {
    background-color: #424242;
    border: 1px solid #4b4b4b;

    .state-text {
      margin: 0;
      padding: 15px;
      // text-align: center;
    }

    .todo-list {
      max-height: 200px;
      overflow: auto;

      ul {
        margin: 0;
        padding: 10px;
        list-style: none;

        li {
          margin-bottom: 5px;
          display: flex;
          align-items: center;
          background-color: #303030;
          border-radius: 5px;
          padding: 10px;
          position: relative;

          label {
            cursor: pointer;
            font-size: 14px;
            display: flex;
            align-items: baseline;
            padding-right: 20px;

            input[type="checkbox"] {
              margin: 0 10px 0 0;
              cursor: pointer;
            }
          }

          &.completed > label {
            opacity: 0.5;
            text-decoration: line-through;
          }

          .remove-todo-button {
            border: none;
            background: none;
            padding: 5px;
            position: absolute;
            right: 10px;
            cursor: pointer;
            // display: none;

            &:disabled {
              opacity: 0.4;
              cursor: not-allowed;
            }

            :global(svg) {
              fill: #666;
            }
          }

          &:hover {
            .remove-todo-button {
              display: block;

              :global(svg) {
                fill: #bd1414;
              }
            }
          }
        }
      }
    }

    .add-todo-form {
      padding: 15px;
      background-color: #303030;
      display: flex;
      flex-wrap: wrap;
      border-top: 1px solid #4b4b4b;

      input {
        flex: 1;
        background-color: #424242;
        border: 1px solid #4b4b4b;
        padding: 10px;
        color: #fff;
        border-radius: 5px;
        margin-right: 10px;
      }
    }
  }
</style>
