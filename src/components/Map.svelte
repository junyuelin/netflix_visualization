<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import * as topojson from 'topojson';
  
  onMount(() => {
    drawMap();
  });

  function drawMap() {
    // Set up your SVG container
    const svg = d3.select('.map-container')
      .append('svg')
      .attr('width', 800)
      .attr('height', 400);

    // Define your projection (e.g., Mercator)
    const projection = d3.geoMercator()
      .scale(100)
      .translate([400, 200]);

    // Define a path generator
    const path = d3.geoPath().projection(projection);

    // Load world map data
    d3.json('https://raw.githubusercontent.com/d3/d3.github.com/master/world-50m.v1.json')
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
