<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';


  let currentIndex = 0;
  let text1 = 'Birth Of Netflix';
  let text2 = 'Subscription Concept Debut';
  let text3 = 'Netflix\'s Streaming Service Announced';
  let text4 = 'Netflix Originals was launched';
  let text5 = 'Netflix started its global expansion-Canada as its first destination';
  let text6 = 'Netflix\'s Introduction of ad-supported tier';
  let text7 = 'Wind-down of DVD';

  let images = [
  {
    src: 'https://github.com/junyuelin/netflix_visualization/blob/1dd35c4ece521947ce1461f2aab35c2725688005/netflix%20static/timeline%20images/p1-1997.png?raw=true',
    year: '1997',
    title: 'Birth Of Netflix',
    details: 'Netflix was founded in 1997 by Reed Hastings and Marc Randolph in Scotts Valley, California. Initially, Netflix offered a DVD rental service by mail.'
  },
  {
    src: 'https://github.com/junyuelin/netflix_visualization/blob/4dfffdc5fff54fe5096c287f2a5129364ac86c96/netflix%20static/timeline%20images/p2-1999.png?raw=true',
    year: '1999',
    title: 'Subscription Concept Debut',
    details: 'In 1999, Netflix introduced a subscription-based model, allowing customers unlimited DVD rentals for a monthly fee, changing the rental business landscape.'
  },
  {
    src: 'https://github.com/junyuelin/netflix_visualization/blob/4dfffdc5fff54fe5096c287f2a5129364ac86c96/netflix%20static/timeline%20images/p3-2007.png?raw=true',
    year: '2007',
    title: 'Netflix\'s Streaming Service Announced',
    details: 'Netflix launched its streaming service in 2007, allowing members to watch television shows and movies on their computers instantly, marking the start of streaming as we know it today.'
  },
  {
    src: 'https://github.com/junyuelin/netflix_visualization/blob/4dfffdc5fff54fe5096c287f2a5129364ac86c96/netflix%20static/timeline%20images/p4-2009.png?raw=true',
    year: '2009',
    title: 'Netflix Originals Launched',
    details: 'Netflix began producing its own original content in 2009. This move was aimed at differentiating its service with exclusive content not available anywhere else.'
  },
  {
    src: 'https://github.com/junyuelin/netflix_visualization/blob/4dfffdc5fff54fe5096c287f2a5129364ac86c96/netflix%20static/timeline%20images/p5-2010.jpg?raw=true',
    year: '2010',
    title: 'Global Expansion Begins',
    details: 'Netflix started its international expansion in 2010, with Canada being the first country outside the US to gain access to Netflix streaming services.'
  },
  {
    src: 'https://github.com/junyuelin/netflix_visualization/blob/4dfffdc5fff54fe5096c287f2a5129364ac86c96/netflix%20static/timeline%20images/p6-ads.jpg?raw=true',
    year: '2022',
    title: 'Ad-Supported Tier Introduced',
    details: 'In 2022, facing subscriber decline and slow-growing revenue, Netflix launched a lower-priced subscription plan that includes advertisements. This move aimed to attract budget-conscious consumers and diversify its revenue streams.'
  },
  {
    src: 'https://github.com/junyuelin/netflix_visualization/blob/4dfffdc5fff54fe5096c287f2a5129364ac86c96/netflix%20static/timeline%20images/p7-2023.png?raw=true',
    year: '2023',
    title: 'Wind-Down of DVD Rentals',
    details: 'Netflix announced in 2023 that it would end its DVD rental service, fully committing to streaming and focusing on its extensive catalog of digital content.'
  }
];

  function showPrev() {
  currentIndex = (currentIndex > 0) ? currentIndex - 1 : images.length - 1;
}

  function showNext() {
      currentIndex = (currentIndex < images.length - 1) ? currentIndex + 1 : 0;
  }


</script>
<h1> Netflix's Major Events Timeline</h1>
<div class="carousel-container">
  <button class="nav-arrow" on:click={showPrev}>⬅️</button>
  <div class="carousel">
    <div
      class="carousel-inner"
      style="transform: translateX(-{currentIndex * 100}%);"
    >
      {#each images as image, index}
      <div class="carousel-item" class:active={index === currentIndex}>
          <!-- Image Container -->
          <div class="image-container">
          <img src={image.src} alt="Year Image" />
          </div>

           <!-- Slider -->
           <div class="slider">
           <div class="slider-indicator" style="left: {currentIndex * (100 / images.length)}%;">
            <!-- Year number positioned above the red point -->
            <div class="year">{image.year}</div>
           </div>
          </div>

          <!-- Description Box -->
          <div class="description">
          <h2>{images[currentIndex].title}</h2> <!-- Title added here -->
          <p>{images[currentIndex].details}</p> <!-- Details remain here -->
        </div>

      </div>
      {/each}
    </div>
  </div>
  <button class="nav-arrow" on:click={showNext}>➡️</button>
</div>

<style>

  h1 {
    margin-top: 40px;
    margin-bottom: 40px; /* Add more space between the heading and the timeline */
  }

  .description {
  background-color: #f9f9f9;
  border: 1px solid #ccc;
  padding: 20px;
  margin-top: 20px;
  text-align: left;
  border-radius: 8px;
  max-width: 800px;
  margin: 20px auto;
}

  .description h2 {
    margin-top: 0;
    text-align: center;
    font-size: 1.5em;
    color: #333;
  }

  .description p {
    margin: 10px 0;
    font-size: 1.2em;
    color: #666;
  }

  .carousel-container {
    display: flex;
    align-items: center;
    justify-content: center;
    max-width: 80%; /*Adjust the maximum width of the carousel */
    margin: auto;
  }
  
  .nav-arrow {
    background-color: #ffffff;
    border: none;
    font-size: 2em;
    cursor: pointer;
  }
  
  .carousel {
    overflow: hidden;
    width: 80%; /* Adjust the width to provide enough space for images */
    display: flex;
    justify-content: center;
  }
  
  .carousel-inner {
    display: flex;
    transition: transform 0.5s ease;
    width: 100%;
  }
  
  .carousel-item {
    min-width: 100%;
    text-align: center;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  .carousel-item.active {
  display: flex; /* Show only the active item */
  }

  .carousel-item img {
  max-width: 100%;
  max-height: 400px; /* Adjust the maximum height of the images */
  object-fit: contain; /* Ensure the image maintains its aspect ratio */
  }
  
  .image-container {
  width: 100%;
  height: 400px; /* Set a fixed height for all images */
  display: flex;
  align-items: center;
  justify-content: center;
  }

  .image-container img {
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
  }

  .slider {
    width: 80%;
    height: 5px;
    background-color: lightgrey;
    position: relative;
    margin: 40px auto; /* Increased the margin to move the slider down */
}

/* Adjusted slider indicator */
.slider-indicator {
    width: 10px;
    height: 10px;
    background-color: red;
    border-radius: 50%;
    position: absolute;
    top: -3px; /* Adjust this if necessary to center the indicator on the slider */
    transition: left 0.5s ease;
}

/* Adjusted year position */
.slider .year {
    position: absolute;
    top: -22px; /* Move the year number down */
    left: 50%;
    transform: translateX(-50%);
    font-size: 1em;
    font-weight: bold;
    color: red;
}

  .text {
  margin-top: 10px;
  font-size: 1.3em;
  font-weight: bold; 
  }
</style>