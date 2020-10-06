<script>
  import Canvas from "./Canvas.svelte";

  let blocks = {};

  const addBlock = () => {
    blocks = {
      ...blocks,
      [Date.now()]: {
        name: "New block",
        x: 60,
        y: 60
      }
    };
  };
</script>

<style>
  button {
    position: absolute;
    top: 10px;
    left: 10px;
    z-index: 1;
  }

  main {
    overscroll-behavior: none;
  }
</style>

<main>
  <button on:click={addBlock}>+</button>
  <Canvas {blocks} />
</main>
