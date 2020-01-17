<script>
  import { onMount } from "svelte";
  import Page from "./Page.svelte";

  export let pages;

  const FPS = 12;

  let pageIndex = 0;

  onMount(() => {
    const interval = setInterval(() => {
      pageIndex = (pageIndex + 1) % pages.length;
    }, 1000 / FPS);

    return () => {
      clearInterval(interval);
    };
  });
</script>

<Page paths={pages[pageIndex]} />
