<script>
  import { connections } from "./connectionsStore";
  import { tick } from 'svelte';

  export let block;
  export let blockId;

  let isConnecting = false;

  const handlePointerDown = () => {
    isConnecting = true;
  };

  const handleBodyPointerMove = ev => {
    if (isConnecting) {
      connections.update(cs => [
        ...cs.filter(c => !c.temp),
        {
          start: blockId,
          end: { x: ev.x, y: ev.y },
          temp: true
        }
      ]);
    }
  };

  const handleBodyPointerUp = async ev => {
    isConnecting = false;
    setTimeout(() => {
      connections.update(cs => [...cs.filter(c => !c.temp)]);
    }, 1);
  };
</script>

<style>
  circle {
    cursor: pointer;
  }
</style>

<svelte:body
  on:pointermove={handleBodyPointerMove}
  on:pointerup={handleBodyPointerUp} />
<circle
  on:pointerdown|stopPropagation={handlePointerDown}
  cx={block.x + 20}
  cy={block.y + 20}
  r={2}
  fill="#17d0d9" />
