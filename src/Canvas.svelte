<script>
  import Connection from "./Connection.svelte";
  import Block from "./Block.svelte";
  import { connections } from "./connectionsStore";

  export let blocks;
  let tempConnection = undefined;
  let selectedBlockId = undefined;

  let screenWidth;
  let viewBox = { x: 0, y: 0, w: window.innerWidth, h: window.innerHeight };
  let oldViewBox = {};
  let startPoint;
  let endPoint;
  $: scale = window.innerWidth / viewBox.w;

  let isDragging = false;

  const handlePointerUp = ev => {
    if (isDragging) {
      isDragging = false;
    }
  };

  const handlePointerDown = ev => {
    selectedBlockId = undefined;
    startPoint = { x: ev.x, y: ev.y };
    oldViewBox = { ...viewBox };
    isDragging = true;
  };

  const handlePointerMove = ev => {
    if (isDragging) {
      endPoint = { x: ev.x, y: ev.y };
      const dx = (startPoint.x - endPoint.x) / scale;
      const dy = (startPoint.y - endPoint.y) / scale;
      viewBox.x = oldViewBox.x + dx;
      viewBox.y = oldViewBox.y + dy;
    }
  };

  const handlePointerLeave = () => {
    isDragging = false;
  };

  const handleMouseWheel = ev => {
    if (isDragging) {
      return;
    }

    const mx = ev.offsetX;
    const my = ev.offsetY;
    const dw = viewBox.w * Math.sign(ev.deltaY) * 0.05;
    const dh = viewBox.h * Math.sign(ev.deltaY) * 0.05;
    const dx = (dw * mx) / window.innerWidth;
    const dy = (dh * my) / window.innerHeight;

    viewBox = {
      x: viewBox.x + dx,
      y: viewBox.y + dy,
      w: viewBox.w - dw,
      h: viewBox.h - dh
    };
  };

  const handleBlockSelect = blockId => {
    selectedBlockId = blockId;
  };
</script>

<style>
  div {
    width: 100%;
    height: 100%;
  }

  svg {
    width: 100%;
    height: 100%;
    cursor: grab;
  }

  svg.dragging {
    cursor: grabbing;
  }
</style>

<div bind:clientWidth={screenWidth}>
  <svg
    on:pointermove={handlePointerMove}
    on:pointerdown={handlePointerDown}
    on:pointerup={handlePointerUp}
    on:pointerleave={handlePointerLeave}
    on:wheel|passive={handleMouseWheel}
    viewBox={`${viewBox.x} ${viewBox.y} ${viewBox.w} ${viewBox.h}`}
    class:dragging={isDragging}>
    {#each $connections as conn}
      <Connection {blocks} connection={conn} {scale} {viewBox} />
    {/each}
    {#each Object.entries(blocks) as [blockId, block]}
      <Block
        {block}
        {blockId}
        {scale}
        isSelected={blockId === selectedBlockId}
        on:select={({ detail: selected }) => handleBlockSelect(selected ? blockId : undefined)} />
    {/each}
  </svg>
</div>
