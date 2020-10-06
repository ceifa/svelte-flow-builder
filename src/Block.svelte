<script>
  import { createEventDispatcher } from "svelte";
  import Connector from "./Connector.svelte";
  import { connections } from "./connectionsStore";

  export let block;
  export let blockId;
  export let isSelected;
  export let scale;

  const dispatch = createEventDispatcher();

  let startPoint = {};
  let endPoint = {};
  let oldBlock = {};

  const handlePointerUp = () => {
    if (isSelected) {
      dispatch("select", false);
    }
    connections.update(conns => [
      ...conns.map(c => {
        if (c.temp) {
          c.temp = false;
          c.end = blockId;
        }
        return c;
      })
    ]);
  };

  const handlePointerDown = ev => {
    dispatch("select", true);
    startPoint = { x: ev.x, y: ev.y };
    oldBlock = { ...block };
  };

  const handlePointerLeave = () => {
    if (isSelected) {
      dispatch("select", false);
    }
  };

  const handlePointerMove = ev => {
    if (isSelected) {
      endPoint = { x: ev.x, y: ev.y };
      const dx = (startPoint.x - endPoint.x) / scale;
      const dy = (startPoint.y - endPoint.y) / scale;
      block.x = oldBlock.x - dx;
      block.y = oldBlock.y - dy;
    }
  };
</script>

<style>
  rect {
    fill: white;
    stroke: black;
    stroke-width: 1px;
    stroke-opacity: 0.2;
  }

  rect.selected {
    stroke: #17d0d9;
    stroke-width: 1px;
    stroke-opacity: 1;
  }

  g {
    z-index: 1;
  }

  g:hover {
    cursor: move;
  }

  text {
    user-select: none;
    text-anchor: middle;
  }
</style>

<g
  on:pointermove={handlePointerMove}
  on:pointerdown|stopPropagation={handlePointerDown}
  on:pointerup={handlePointerUp}
  on:pointerleave={handlePointerLeave}>
  <rect
    x={block.x}
    y={block.y}
    width="40px"
    height="20px"
    rx="1"
    ry="1"
    class:selected={isSelected} />
  <text
    x={block.x + 20}
    y={block.y + 10 + 2}
    font-family="Nunito Sans,Helvetica,sans-serif"
    font-size="4"
    fill="#607b99">
    {block.name}
  </text>
  <Connector {blockId} {block} />
</g>
