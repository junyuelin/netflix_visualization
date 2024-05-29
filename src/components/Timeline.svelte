<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';


    let currentIndex = 0;
    let images = [
        { src: 'https://github.com/junyuelin/netflix_visualization/blob/1dd35c4ece521947ce1461f2aab35c2725688005/netflix%20static/timeline%20images/p1-1997.png?raw=true', year: '1997' , text:'text1'},
        { src: 'https://github.com/junyuelin/netflix_visualization/blob/613007d90b5521816b79dc0bbe1cf959fc5d81e8/netflix%20static/timeline%20images/p2-1999.png?raw=true', year: '1999', text: 'text2' },
        { src: 'https://github.com/junyuelin/netflix_visualization/blob/1dd35c4ece521947ce1461f2aab35c2725688005/netflix%20static/timeline%20images/p1-1997.png?raw=true', year: '3', text: 'text3'},
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

            <!-- Text -->
            <div class="text">{image.text}</div>

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
    margin: 20px auto;
    }

    .slider-indicator {
    width: 10px;
    height: 10px;
    background-color: red;
    border-radius: 50%;
    position: absolute;
    top: -3px; /* Adjust to center the indicator */
    transition: left 0.5s ease;
    }

    .slider .year {
    position: absolute;
    top: -30px; /* Adjust this value to position the year number above the indicator */
    left: 50%;
    transform: translateX(-50%);
    font-size: 1em;
    font-weight: bold;
    color: red;
    }

    .text {
    margin-top: 10px;
    font-size: 1em;
    }
  </style>