<script lang="ts">
  type Pos = { x: number; y: number };
  type Node = Pos & { v: string };
  let pos: Pos = { x: 0, y: 0 };
  let nodes: Node[] = [
    { x: 0, y: 0, v: "X" },
    { x: 1, y: 1, v: "Y" },
    { x: 2, y: 2, v: "Z" },
  ];

  function posEqual(a: Pos, b: Pos): boolean {
    return a.x == b.x && a.y == b.y;
  }

  type Dir = "up" | "left" | "right" | "down";
  const MAX_X = 20;
  const MAX_Y = 20;

  function movePos(cur: Pos, dir: Dir): Pos {
    let newPos = {
      x: dir === "right" ? cur.x + 1 : dir === "left" ? cur.x - 1 : cur.x,
      y: dir === "up" ? cur.y - 1 : dir === "down" ? cur.y + 1 : cur.y,
    };
    if (newPos.x < 0 || newPos.y < 0 || newPos.x > MAX_X || newPos.y > MAX_Y) {
      return cur;
    } else {
      return newPos;
    }
  }

  function onKeyDown(e: KeyboardEvent) {
    if (/^Arrow.*$/.test(e.key)) {
      e.preventDefault();
      let dir = e.key.replace("Arrow", "").toLowerCase() as Dir;
      pos = movePos(pos, dir);
    }
  }
</script>

<svelte:window on:keydown={onKeyDown} />
<div class="grid">
  {#each nodes as node}
    <div
      class="item"
      class:active={posEqual(pos, node)}
      style="grid-column: {node.x + 1}; grid-row: {node.y + 1};"
    >
      {node.v}
    </div>
  {/each}
</div>

<style>
  .grid {
    display: grid;
    grid-template-columns: repeat(20, 1fr);
    grid-template-rows: repeat(20, 1fr);
    align-content: center;
    justify-content: space-evenly;
    align-items: stretch;
    justify-items: stretch;
  }

  .active {
    background-color: lightblue;
  }
</style>
