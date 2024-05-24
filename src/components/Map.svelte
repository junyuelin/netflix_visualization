<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import * as topojson from 'topojson-client';

  onMount(() => {
    drawMap();
  });

  function drawMap() {
    // Set up your SVG container
    const svg = d3.select('.map-container')
      .append('svg')
      .attr('width', 1200)
      .attr('height', 800);

    // Define your projection (e.g., Natural Earth)
    const projection = d3.geoNaturalEarth1() // Using a different projection for a different map
      .scale(150)
      .translate([600, 400]); // Adjusted translation for centering

    // Define a path generator
    const path = d3.geoPath().projection(projection);

    // Load world map data
    d3.json('https://raw.githubusercontent.com/d3/d3.github.com/master/world-110m.v1.json')
      .then(function(world) {
        // Draw the map
        svg.selectAll('path')
          .data(topojson.feature(world, world.objects.countries).features)
          .enter().append('path')
          .attr('d', path)
          .attr('fill', 'lightgray')
          .attr('stroke', 'white');
      });
  }
</script>

<div class="map-container"></div>

<style>
  .map-container {
    width: 100%;
    height: 100%;
  }
</style>
