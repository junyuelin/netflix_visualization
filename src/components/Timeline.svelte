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
        { src: 'https://github.com/junyuelin/netflix_visualization/blob/1dd35c4ece521947ce1461f2aab35c2725688005/netflix%20static/timeline%20images/p1-1997.png?raw=true', year: '1997' , text: text1},
        { src: 'https://github.com/junyuelin/netflix_visualization/blob/4dfffdc5fff54fe5096c287f2a5129364ac86c96/netflix%20static/timeline%20images/p2-1999.png?raw=true', year: '1999', text: text2 },
        { src: 'https://github.com/junyuelin/netflix_visualization/blob/4dfffdc5fff54fe5096c287f2a5129364ac86c96/netflix%20static/timeline%20images/p3-2007.png?raw=true', year: '2007', text: text3},
        { src: 'https://github.com/junyuelin/netflix_visualization/blob/4dfffdc5fff54fe5096c287f2a5129364ac86c96/netflix%20static/timeline%20images/p4-2009.png?raw=true', year: '2009', text: text4},
        { src: 'https://github.com/junyuelin/netflix_visualization/blob/4dfffdc5fff54fe5096c287f2a5129364ac86c96/netflix%20static/timeline%20images/p5-2010.jpg?raw=true', year: '2010', text: text5},
        { src: 'https://github.com/junyuelin/netflix_visualization/blob/4dfffdc5fff54fe5096c287f2a5129364ac86c96/netflix%20static/timeline%20images/p6-ads.jpg?raw=true', year: '2022', text: text6},
        { src: 'https://github.com/junyuelin/netflix_visualization/blob/4dfffdc5fff54fe5096c287f2a5129364ac86c96/netflix%20static/timeline%20images/p7-2023.png?raw=true', year: '2023', text: text7},
        // Add more images as needed
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

            <!-- Text -->
            <div class="text">
              <a href={image.link}>{image.text}</a>
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
    font-size: 1.3em;
    font-weight: bold; 
    }
  </style>