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
</script>

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
    justify-content: center;
    align-items: center;
    justify-items: center;
  }

  .active {
    background-color: lightblue;
  }
</style>
