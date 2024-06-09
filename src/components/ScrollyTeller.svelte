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
      <h1 class="title">
        The Evolution Story of the Streaming Giant Netflix: <br> 
        Adapting Profit Models in a Rapidly Changing Era
      </h1> 
    <div class="bullet-points">
    <ul>
      <li>Key Milestones in Netflix's History</li>
      <li>Overview of Streaming Industry Growth</li>
      <li>Global Expansion Strategy</li>
      <li>Original Content strategy</li>
      <li>Launch of the Ad-Supported Tier</li>
    </ul>
  </div>
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
    <section id="takeaway">
      <!-- Include the Takeaway within the 8th section -->
      <h2>Takeaway</h2>
    <p>
      Netflix began its journey in 1997 as a DVD rental service. 
      By 2007, recognizing the shift towards digital consumption, Netflix launched its streaming service. 
      This transition from physical media to digital streaming provided greater convenience and access, 
      significantly boosting its revenue and audience reach.
    </p>

    <p>
      Starting in 2010, Netflix embarked on an ambitious international expansion. 
      This expansion into Latin America, Europe, and Asia-Pacific was crucial for increasing its subscriber base. 
      Despite facing challenges like regulatory hurdles and the need to adapt content to diverse cultural preferences, 
      this move established Netflix as a global entertainment leader.
    </p>

    <p>
      A major turning point came in 2012 with the introduction of original content. 
      By creating its own shows such as "House of Cards" and "Stranger Things," 
      Netflix differentiated itself from competitors. This strategic shift reduced its reliance on licensed content, 
      which was both costly and uncertain. Investing in original productions gave Netflix a unique competitive edge, 
      driving subscriber growth and retention by offering exclusive, high-quality content.
    </p>

    <p>
    By 2022, Netflix encountered new challenges.
    The rapid growth from international expansion and original content began to plateau. 
    Economic pressures, including inflation, and a saturated market with a fixed viewer base necessitated a new strategy. 
    In response, Netflix introduced an ad-supported tier (AVOD) in 2022. 
    This model aimed to attract cost-sensitive customers while generating additional revenue through advertisements. 
    The introduction of the AVOD tier showcased Netflix's ability to adapt its business model to 
    changing market dynamics and economic conditions.
     </p> 

    <p> 
    Netflix's evolution underscores the importance of innovation and strategic adaptation in navigating competitive markets. 
    </p>
    <p>
    Looking ahead, what should Netflix do to maintain its competitive edge in the streaming war?
    <p>
    <p>
    The key takeaway is Netflix's successful adaptation through strategic content creation and profit model evolution. 
    Our interactive visualizations clearly demonstrate these impactful decisions at different times, aligning perfectly with our project's theme.
    First, we use a timeline to introduce the history, then we use different interactive charts to demonstrate the innovative decisions.
    We hope that our visualizations convey the spirit of keeping pace with the times to our audience.
    <p>


    </p>

      <h2>Source</h2>
      <p>Statista, Netflix's 10K report</p>
      
      <h2>Source Code</h2>
      <p><a href="https://github.com/junyuelin/netflix_visualization/tree/main">Github</a></p>
    </section>
  </div>
</Scroller>

<style>
  #takeaway {
    padding: 20px;
    background-color: #f0f0f0;
    margin-top: 20px;
    border-radius: 8px;
  }
  #takeaway p {
    font-size: 18px;
    line-height: 1.5;
    color: black;
    margin-bottom: 15px;
    padding: 0 200px;
  }
  .background {
    width: 100%; /* Set the same width as .foreground */
    margin: 0 auto; /* Center the element */
    height: 100vh; /* Full viewport height */
    position: relative;
  }

  .foreground {
    width: 100%; /* Set the same width as .background */
    margin: 0 auto; /* Center the element */
    height: auto;
    position: relative;
  }

  .first-session {
    position: relative; /* Ensure the overlay is positioned correctly */
    height: 100vh; /* Ensure the first session covers the full viewport height */
    display: flex;
    align-items: center; /* Center align the title vertically */
    justify-content: center; /* Center align the title horizontally */
    flex-direction: column; /* Align content in column */
    text-align: center; /* Center text */
  }
  .bullet-points {
    margin-top: 20px; /* Adjust margin-top as needed */
    text-align: left; /* Align bullet points to the left */
  }

  .bullet-points ul {
    list-style-type: disc; /* Use disc bullets */
    padding-left: 20px; /* Add left padding to the bullet points */
    color: white; 
    font-size: 16px;
    font-family: Arial, sans-serif;
    margin-top: 20vh;
  }

  .bullet-points li {
    margin-bottom: 10px; /* Adjust margin between bullet points */
  }

  .first-session::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-image: url('https://github.com/junyuelin/netflix_visualization/blob/main/netflix%20static/Netflix_background.jpeg?raw=true');
    background-size: cover;
    background-position: center;
    opacity: 0.9; /* Adjust the opacity as needed */
    z-index: -1; /* Ensure the overlay is behind the content */
  }

  .title {
    color: white;
    text-align: center;
    font-size: 40px; /* Increased the font size */
    margin-top: -15vh; /* Adjusted margin-top to make the title more visible */
    line-height: 1.2; /* Adjust line height for better spacing */
    position: relative; /* Ensure the title is positioned correctly */
    z-index: 1; /* Ensure the title is above the overlay */
    padding: 20px; /* Padding around the text */
    border-radius: 10px; /* Rounded corners */
  }

  section {
    height: 100vh;
    text-align: center;
    width: 100%; /* Adjust the width to match the background and foreground */
    color: black;
    padding: 1em;
    margin: 0 auto; /* Center the section */
  }
  #revenue {
    background-color: #f0f0f0;
  }

  #svod {
    background-color: #f0f0f0;
  }
  #original {
    background-color: #f0f0f0;
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