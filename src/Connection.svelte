<script>
  export let blocks;
  export let connection;
  export let scale;
  export let viewBox;

  $: ({ start, end } = (() => {
    let end;
    if (typeof connection.end === "object") {
      end = {
        x: connection.end.x / scale + viewBox.x,
        y: connection.end.y / scale + viewBox.y
      };
    } else {
      end = {
        x: blocks[connection.end].x + 20,
        y: blocks[connection.end].y + 10
      };
    }

    return {
      start: {
        x: blocks[connection.start].x + 20,
        y: blocks[connection.start].y + 10
      },
      end
    };
  })());
</script>

<polyline
  points="{start.x},{start.y}
  {start.x},{end.y}
  {end.x},{end.y}"
  stroke="#52636c"
  fill="none" />
