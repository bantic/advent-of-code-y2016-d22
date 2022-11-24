<script lang="typescript">
  import nodesJson from "./nodes.json";

  type Pos = { x: number; y: number };
  type Node = Pos & { v: string };

  let nodes = nodesJson;

  const maxX = Math.max(...nodes.map((n) => n.x));
  const maxY = Math.max(...nodes.map((n) => n.y));
  const minY = Math.min(...nodes.map((n) => n.y));

  let goalNode = nodeFromPos({ x: maxX, y: minY });
  goalNode.v = "G";

  let startNode = nodes.find((n) => n.v === "_");
  let startPos: Pos = { x: startNode.x, y: startNode.y };

  let goalAchieved = false;

  let pos = startPos;

  let moves = 0;

  function posEqual(a: Pos, b: Pos): boolean {
    return a.x == b.x && a.y == b.y;
  }

  type Dir = "up" | "left" | "right" | "down";

  function nodeFromPos(p: Pos): Node {
    return nodes.find((n) => n.x == p.x && n.y == p.y);
  }

  function isValidPos(p: Pos): boolean {
    let toNode = nodeFromPos(p);
    if (!toNode) {
      return false;
    }
    if (toNode.v === "#") {
      return false;
    }
    return true;
  }

  let previousStates = [];

  function movePos(cur: Pos, dir: Dir): Pos {
    let newPos = {
      x: dir === "right" ? cur.x + 1 : dir === "left" ? cur.x - 1 : cur.x,
      y: dir === "up" ? cur.y - 1 : dir === "down" ? cur.y + 1 : cur.y,
    };
    if (!isValidPos(newPos)) {
      return cur;
    }
    previousStates.push({
      nodes: nodes
        .map((n) => {
          return { ...n };
        })
        .slice(),
      pos: { ...cur },
      moves,
      goalAchieved,
    });
    moves += 1;
    let curNode = nodeFromPos(cur);
    let newNode = nodeFromPos(newPos);
    let tmp = newNode.v;
    newNode.v = curNode.v;
    curNode.v = tmp;
    nodes = nodes;
    if (nodeFromPos({ x: 0, y: 0 }).v === "G") {
      goalAchieved = true;
    }
    return newPos;
  }

  function undo() {
    if (previousStates.length > 0) {
      let prevState = previousStates.pop();
      nodes = prevState.nodes;
      pos = prevState.pos;
      moves = prevState.moves;
      goalAchieved = prevState.goalAchieved;
    }
  }

  function onKeyDown(e: KeyboardEvent) {
    if (e.key === "z" && e.metaKey) {
      e.preventDefault();
      undo();
    } else if (/^Arrow.*$/.test(e.key)) {
      e.preventDefault();
      let dir = e.key.replace("Arrow", "").toLowerCase() as Dir;
      pos = movePos(pos, dir);
    } else if (e.key == "j") {
      // shortcut
      e.preventDefault();
      let dirs: Dir[] = ["down", "left", "left", "up", "right"];
      for (let dir of dirs) {
        pos = movePos(pos, dir);
      }
    }
  }
</script>

<svelte:window on:keydown={onKeyDown} />
<div class="instructions">
  <a href="https://github.com/bantic/advent-of-code-y2016-d22"
    >Instructions here</a
  >.
</div>
<div class="details">
  Moves: <span class:goal-achieved={goalAchieved}>{moves}</span>
</div>
<div
  class="grid"
  class:goal-achieved={goalAchieved}
  style="grid-template-columns: repeat({maxX}, 1fr); grid-template-rows: repeat({maxY}, 1fr);"
>
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
    align-content: center;
    justify-content: space-evenly;
    align-items: stretch;
    justify-items: stretch;
  }

  .grid.goal-achieved {
    outline: 5px solid gold;
    background-color: yellow;
  }

  .details .goal-achieved {
    font-weight: bold;
  }

  .active {
    background-color: lightblue;
  }
</style>
