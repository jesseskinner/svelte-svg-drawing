<script>
  export let paths;
  export let stroke = 'black';

  function getPath(path) {
    const first = path[0];
    const rest = path.slice(1);

    return `M${first.x} ${first.y} ${rest.map(({ x, y }) => `L${x} ${y} `)} M${
      first.x
    } ${first.y}`;
  }
</script>

<style>
  svg {
    display: block;
    width: 100%;
    height: 100%;
  }
</style>

<svg>
  {#each paths as path}
    <path
      d={getPath(path)}
      stroke-width="5"
      fill="transparent"
      {stroke} />
  {/each}
</svg>
