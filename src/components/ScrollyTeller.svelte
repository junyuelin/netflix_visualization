<script>
  import Scroller from "./Scroller.svelte";
  import Revenue from "./Revenue.svelte";
  import Map from "./Map.svelte";
  import Timeline from "./Timeline.svelte";
  import SVOD from "./SVOD.svelte";
  import AVOD from "./AVOD.svelte";
  import Original from "./Original.svelte";
  import Subscriber from "./Subscriber.svelte";

  let index;
</script>
<Scroller
  top={0.0}
  bottom={1}
  threshold={0.5}
  bind:index

>
  <div class="background" slot="background">
    
  </div>

  <div class="foreground" slot="foreground">
    <section class="first-session">
      <h1 class="title">The Evolution Story of the Streaming Giant Netflix: Adapting Profit Models in a Rapidly Changing Era</h1> 
    </section>
    <section>
      <!-- Include the Timeline component within the 1st session -->
      <Timeline />
    </section>
    <section>
      <!-- Include the Revenue component within the 2nd section -->
      <Revenue {index} />
    </section>
    <section>
      <!-- Include the Subscriber component within the 3rd section -->
      <Subscriber {index} />
    </section>
    <section>
      <!-- Include the SVOD component within the 4th section -->
      <SVOD />
    </section>
    <section>
      <!-- Include the Map component within the 5th section -->
      <Map />
    </section>
    <section>
      <!-- Include the Map component within the 6th section -->
      <Original />
    </section>
    <section>
      <!-- Include the Map component within the 7th section -->
      <AVOD />
    </section>
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
    width: 80%;
    margin: 0 auto;
    height: auto;
    outline: blue solid 3px;
    position: relative;
  }

   .first-session {
    background-image: url('https://github.com/junyuelin/netflix_visualization/blob/main/netflix%20static/Netflix_background.jpeg?raw=true');
    background-size: cover;
    background-position: center;
  }
   .title {
    color: white;
    text-align: center;
    font-size: 25px; /* Adjust the font size as needed */
    margin-top: 50px; /* Adjust the margin-top as needed */
  }
  section {
    height: 80vh;
    outline: magenta solid 3px;
    text-align: center;
    width: 1180px; /* adjust at will */
    color: black;
    padding: 1em;
    margin: 0 0 2em 0; /* Set left and right margins to auto */
  }

</style>