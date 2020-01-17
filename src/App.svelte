<script>
  import { onMount } from "svelte";
  import Page from "./Page.svelte";
  import Player from "./Player.svelte";

  let rootElement;
  let offsetX = 0;
  let offsetY = 0;
  let paths = [];
  let pages = [];
  let isMouseDown = false;
  let pageIndex = 0;
  let isPlaying = false;

  $: console.log(offsetX, offsetY);

  onMount(() => {
    const { x, y } = rootElement.getBoundingClientRect();
    offsetX = x;
    offsetY = y;
  });

  function onMouseDown(event) {
    const [x, y] = getXY(event);

    paths.push([{ x, y }]);
    paths = paths;

    isMouseDown = true;
  }

  function onMouseUp() {
    isMouseDown = false;
  }

  function onMouseMove(event) {
    if (isMouseDown) {
      const [x, y] = getXY(event);
      const currentPath = paths[paths.length - 1];
      const lastPoint = currentPath[currentPath.length - 1];

      if (lastPoint.x !== x || lastPoint.y !== y) {
        currentPath.push({ x, y });
        paths = paths;
      }
    }
  }

  function getXY(event) {
    const x = Math.round(event.clientX - offsetX);
    const y = Math.round(event.clientY - offsetY);

    return [x, y];
  }

  function onLastPage() {
    if (pageIndex > 0) {
      pages[pageIndex] = paths;
      pageIndex--;
      paths = pages[pageIndex];
    }
  }

  function onNextPage() {
    pages[pageIndex] = paths;
    pageIndex++;
    paths = pages[pageIndex] || [];
  }

  function onPlayToggle() {
    isPlaying = !isPlaying;
  }
</script>

<style>
  :global(html),
  :global(body) {
    background: #aaa;
    padding: 0;
    border: 0;
  }

  header {
    position: absolute;
    top: 0;
    right: 5vw;
  }

  header button {
    font-size: 11px;
  }

  div {
    width: 90vw;
    height: 85vh;
    background: #fff;
    position: absolute;
    top: 7.5vh;
    left: 5vw;
  }

  section {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
  }
</style>

<header>
  <button on:click={onLastPage}>&lt; Last Page</button>
  <button on:click={onNextPage}>Next Page &gt;</button>
  <button on:click={onPlayToggle}>
    {#if isPlaying}Edit{:else}Play{/if}
  </button>
</header>

{#if isPlaying}
  <div>
    <Player {pages} />
  </div>
{:else}
  <div
    bind:this={rootElement}
    on:mousedown={onMouseDown}
    on:mousemove={onMouseMove}
    on:mouseup={onMouseUp}>

    {#if pageIndex > 1}
      <section>
        <Page paths={pages[pageIndex - 2]} stroke="#ddd" />
      </section>
    {/if}

    {#if pageIndex > 0}
      <section>
        <Page paths={pages[pageIndex - 1]} stroke="#999" />
      </section>
    {/if}

    <section>
      <Page {paths} />
    </section>
  </div>
{/if}
