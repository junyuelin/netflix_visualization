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

  // Function to handle smooth scrolling
  function scrollToSection(event) {
    event.preventDefault();
    const sectionId = event.target.getAttribute("href").substring(1);
    document.getElementById(sectionId).scrollIntoView({ behavior: "smooth" });
  }
</script>

<nav class="content-menu">
  <ul>
    <li><a href="#first-session" on:click={scrollToSection}>Introduction</a></li>
    <li><a href="#timeline" on:click={scrollToSection}>Timeline</a></li>
    <li><a href="#revenue" on:click={scrollToSection}>Revenue</a></li>
    <li><a href="#subscriber" on:click={scrollToSection}>Subscribers</a></li>
    <li><a href="#svod" on:click={scrollToSection}>SVOD</a></li>
    <li><a href="#map" on:click={scrollToSection}>Map</a></li>
    <li><a href="#original" on:click={scrollToSection}>Originals</a></li>
    <li><a href="#avod" on:click={scrollToSection}>AVOD</a></li>
  </ul>
</nav>

<Scroller
  top={0.0}
  bottom={1}
  threshold={0.5}
  bind:index
>
  <div class="background" slot="background">
    
  </div>

  <div class="foreground" slot="foreground">
    <section id="first-session" class="first-session">
      <h1 class="title">The Evolution Story of the Streaming Giant Netflix: Adapting Profit Models in a Rapidly Changing Era</h1> 
    </section>
    <section id="timeline">
      <!-- Include the Timeline component within the 1st session -->
      <Timeline />
    </section>
    <section id='revenue'>
      <!-- Include the Revenue component within the 2nd section -->
      <Revenue {index} />
    </section>
    <section id="subscriber">
      <!-- Include the Subscriber component within the 3rd section -->
      <Subscriber {index} />
    </section>
    <section id="svod">
      <!-- Include the SVOD component within the 4th section -->
      <SVOD />
    </section>
    <section id="map">
      <!-- Include the Map component within the 5th section -->
      <Map />
    </section>
    <section id="original">
      <!-- Include the Original component within the 6th section -->
      <Original />
    </section>
    <section id="avod">
      <!-- Include the AVOD component within the 7th section -->
      <AVOD />
    </section>
  </div>
</Scroller>

<style>
  .background {
    width: 100%;
    height: 100vh;
    position: relative;
  }

  .foreground {
    width: 80%;
    margin: 0 auto;
    height: auto;
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
    font-size: 25px;
    margin-top: 50px;
  }

  section {
    height: 80vh;
    text-align: center;
    width: 1180px;
    color: black;
    padding: 1em;
  }

  .content-menu {
    position: fixed;
    top: 0;
    width: 100%;
    background: rgba(255, 255, 255, 0.8);
    padding: 10px 0;
    z-index: 1000;
    text-align: center;
  }

  .content-menu ul {
    list-style-type: none;
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: center;
  }

  .content-menu ul li {
    margin: 0 10px;
  }

  .content-menu ul li a {
    text-decoration: none;
    color: black;
    font-weight: bold;
  }

  .content-menu ul li a:hover {
    text-decoration: underline;
  }
</style>