<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import * as topojson from 'topojson-client';

  onMount(() => {
    drawMap();
  });

  function drawMap() {
    const width = 1200;
    const height = 800;
    const svg = d3.select('.map-container')
      .append('svg')
      .attr('width', width)
      .attr('height', height);

    const projection = d3.geoNaturalEarth1()
      .scale(150)
      .translate([width / 2, height / 2]);

    const path = d3.geoPath().projection(projection);

    const slider = d3.select('#year-slider');
    const yearValue = d3.select('#year-value');

    // Create a tooltip element
    const tooltip = d3.select('body').append('div')
      .attr('class', 'tooltip')
      .style('position', 'absolute')
      .style('visibility', 'hidden')
      .style('background', '#fff')
      .style('border', '1px solid #ccc')
      .style('padding', '10px')
      .style('border-radius', '5px');

    Promise.all([
      d3.json('https://raw.githubusercontent.com/holtzy/D3-graph-gallery/master/DATA/world.geojson'),
      d3.csv('https://raw.githubusercontent.com/junyuelin/netflix_visualization/main/netflix%20static/expansion/expansion_countries.csv')
    ]).then(([mapData, countryData]) => {
      svg.append('g')
        .selectAll('path')
        .data(mapData.features)
        .enter().append('path')
        .attr('d', path)
        .attr('class', d => 'country ' + d.properties.name)
        .attr('fill', 'lightgray')
        .attr('stroke', 'white')
        .on('mouseover', (event, d) => {
          tooltip.style('visibility', 'visible').text(d.properties.name);
        })
        .on('mousemove', event => {
          tooltip.style('top', (event.pageY - 10) + 'px').style('left', (event.pageX + 10) + 'px');
        })
        .on('mouseout', () => {
          tooltip.style('visibility', 'hidden');
        });

      function updateMap(year) {
        const filteredData = countryData.filter(d => +d.year <= +year);

        let highlightedCountries = new Set(); // Clear the highlighted countries for each update

        filteredData.forEach(d => {
          highlightedCountries.add(d.name);
        });

        svg.selectAll('.country')
          .attr('fill', d => {
            return highlightedCountries.has(d.properties.name) ? 'steelblue' : 'lightgray';
          });
      }

      updateMap(slider.property('value'));

      slider.on('input', function() {
        const year = this.value;
        yearValue.text(year);
        updateMap(year);
      });
    });
  }
</script>

<div class="container">
  <div class="map-container"></div>
  <div class="slider-container">
    <input type="range" min="1997" max="2016" step="1" value="2010" id="year-slider">
    <span id="year-value">2011</span>
  </div>
  <div class="text-container">
    <p>This map shows the expansion of Netflix across different countries over the years. Use the slider to see the progression by year.</p>
  </div>
</div>

<style>
  .container {
    margin-top: -100px; /* Adjust as needed */
  }
  .map-container {
    width: 100%;
    height: 600px;
  }
  .slider-container {
    margin: 20px;
  }
  :global(.tooltip) {
    position: absolute;
    background: #fff;
    border: 1px solid #ccc;
    padding: 10px;
    border-radius: 5px;
    color: black
  }
</style>
