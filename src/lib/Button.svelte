<script>
  export let size = "small";
  export let shadow = false;
  export let bgColor = undefined;
  export let textColor = undefined;

  let isLeftHovered;
</script>

<button
  on:click
  style:--buttonBgColor={bgColor}
  style:--buttonTextColor={textColor}
  class:size-lg={size === "large"}
  class:size-sm={size === "small"}
  class:shadow
  {...$$restProps}
>
  {#if $$slots.leftContent}
    <div
      class="left-content"
      on:mouseenter={() => (isLeftHovered = true)}
      on:mouseleave={() => (isLeftHovered = false)}
      role="button"
      tabindex="-1"
    >
      <slot name="leftContent" {isLeftHovered} />
    </div>
  {/if}
  <slot {isLeftHovered}>Fallback</slot>
</button>

<style lang="scss">
  @use "../styles/variables.scss";
  button {
    display: flex;
    align-items: center;
    border: none;
    background-color: var(--buttonBgColor);
    color: var(--buttonTextColor);
    padding: 15px 20px;
    font-weight: bold;
    border-radius: 5px;
    cursor: pointer;
    .left-content {
      margin-right: 10px;
    }
    &:hover {
      background-image: linear-gradient(rgba(0, 0, 0, 0.4) 0 0);
    }
    &:active {
      background-image: linear-gradient(rgba(255, 255, 255, 0.1) 0 0);
    }
    &:disabled {
      cursor: not-allowed;
    }
    &.size-sm {
      padding: 15px 20px;
    }
    &.size-lg {
      padding: 25px 40px;
    }
    &.shadow {
      box-shadow: 0 0 10px rgba(1, 1, 1, 1);
    }
  }
</style>
