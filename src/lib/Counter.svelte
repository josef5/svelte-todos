<script>
  export let initialCount;
  export let maxCount = 10;

  let count = initialCount;

  $: string = `You have clicked me ${count} times`;
  // console.log(`The count is ${count}`);

  console.log($$props, $$restProps);

  function increment() {
    if (count < maxCount) {
      count++;
    }
  }
</script>

<button on:click={increment}>Clicks {count}</button>
<h3>{string}</h3>

<style>
  button {
    padding: 30px;
  }
</style>
