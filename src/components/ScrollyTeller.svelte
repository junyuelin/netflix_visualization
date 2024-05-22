<script>
  import Scroller from "./Scroller.svelte";
  import Revenue from "./Revenue.svelte";

  let index;
</script>
<Scroller
  top={0.0}
  bottom={1}
  threshold={0.5}
  bind:index

>
  <div class="background" slot="background">
    <Revenue {index} />

  </div>

  <div class="foreground" slot="foreground">
    <section>This is the first section.</section>
    <section>This is the second section.</section>
    <section>This is the third section.</section>
    <section>This is the fourth section.</section>
  </div>
</Scroller>

<style>
  .background {
    width: 100%;
    height: 100vh;
    position: relative;
    outline: green solid 3px;
  }

  .foreground {
    width: 50%;
    margin: 0 auto;
    height: auto;
    position: relative;
  }


  section {
    height: 80vh;
    outline: magenta solid 3px;
    text-align: center;
    max-width: 750px; /* adjust at will */
    color: black;
    padding: 1em;
    margin: 0 0 2em 0;
  }
</style>