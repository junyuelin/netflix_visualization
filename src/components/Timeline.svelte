<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';


    let currentIndex = 0;
    let images = [
        { src: 'https://github.com/junyuelin/netflix_visualization/blob/1dd35c4ece521947ce1461f2aab35c2725688005/netflix%20static/timeline%20images/p1-1997.png?raw=true', year: 'Year 1997' },
        { src: 'https://github.com/junyuelin/netflix_visualization/blob/1dd35c4ece521947ce1461f2aab35c2725688005/netflix%20static/timeline%20images/p1-1997.png?raw=true', year: 'Year 2' },
        // Add more images as needed
    ];

    function showPrev() {
    currentIndex = (currentIndex > 0) ? currentIndex - 1 : images.length - 1;
  }

    function showNext() {
        currentIndex = (currentIndex < images.length - 1) ? currentIndex + 1 : 0;
    }


</script>

<div class="carousel-container">
    <button class="nav-arrow" on:click={showPrev}>⬅️</button>
    <div class="carousel">
      <div
        class="carousel-inner"
        style="transform: translateX(-{currentIndex * 100}%);"
      >
        {#each images as image}
          <div class="carousel-item">
            <img src={image.src} alt="Year Image" />
            <div id="year">{image.year}</div>
          </div>
        {/each}
      </div>
    </div>
    <button class="nav-arrow" on:click={showNext}>➡️</button>
  </div>

<style>
    .carousel-container {
      display: flex;
      align-items: center;
      justify-content: center;
      max-width: 80%; /*Adjust the maximum width of the carousel */
      /* margin: auto; */
    }
    
    .nav-arrow {
      background-color: #ffffff;
      border: none;
      font-size: 2em;
      cursor: pointer;
    }
    
    .carousel {
      overflow: hidden;
      width: 300px;
    }
    
    .carousel-inner {
      display: flex;
      transition: transform 0.5s ease;
    }
    
    .carousel-item {
      min-width: 100%;
      text-align: center;
    }
    
    #year {
      margin-top: 10px;
      font-size: 1.5em;
      font-weight: bold;
    }
  </style>