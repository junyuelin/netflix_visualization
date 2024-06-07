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
      const countryYearMap = new Map();
      countryData.forEach(d => {
        countryYearMap.set(d.name, d.year);
      });

      svg.append('g')
        .selectAll('path')
        .data(mapData.features)
        .enter().append('path')
        .attr('d', path)
        .attr('class', d => 'country ' + d.properties.name)
        .attr('fill', 'lightgray')
        .attr('stroke', 'white')
        .attr('stroke-width', 0.5)
        .on('mouseover', (event, d) => {
          const countryName = d.properties.name;
          const year = countryYearMap.get(countryName);
          const tooltipText = year ? `Country Name: ${countryName}<br>Available Year: ${year}` : `${countryName}<br>Available Year: N/A`;
          tooltip.style('visibility', 'visible').html(tooltipText);
          d3.select(event.currentTarget).attr('class', 'country highlight');
        })
        .on('mousemove', event => {
          tooltip.style('top', (event.pageY - 10) + 'px').style('left', (event.pageX + 10) + 'px');
        })
        .on('mouseout', (event, d) => {
          tooltip.style('visibility', 'hidden');
          d3.select(event.currentTarget).attr('class', 'country');
        });

      function updateMap(year) {
        const filteredData = countryData.filter(d => +d.year <= +year);

        let highlightedCountries = new Set();

        filteredData.forEach(d => {
          highlightedCountries.add(d.name);
        });

        svg.selectAll('.country')
          .attr('fill', d => {
            return highlightedCountries.has(d.properties.name) ? 'steelblue' : 'lightgray';
          });
      }

      slider.on('input', function() {
        const year = this.value;
        yearValue.text(year);
        updateMap(year);
      });

      // Initial update
      updateMap(slider.property('value'));
    });
  }
</script>

<div class="container">
  <div class="map-container"></div>
</div>
<div class="slider-container_map">
  <input type="range" min="1997" max="2016" step="1" value="1997" id="year-slider">
  <span id="year-value">1997</span>
</div>
<div class="text-container">
  <div class="title">Expansion</div>
  <div class="description">
    <ul>
      <li>2010: Expansion to Canada.</li>
      <li>2011: Launch of streaming services in Latin America and the Caribbean.</li>
      <li>2012: Expansion to the UK and Ireland.</li>
      <li>2013: Expansion to Nordic countries.</li>
      <li>2016: Netflix becomes available globally.</li>
    </ul>
    <p>By expanding globally, Netflix grows its market size and continually increases its subscriber base.</p>
  </div>
</div>

<style>
  .container {
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: -100px;
  }
  .map-container {
    width: 70%;
    height: 600px;
    transform: translateX(-10%);
  }
  .text-container {
    display: inline-block;
    padding: 15px;
    background-color: #f9f9f9;
    border: 1px solid #ccc;
    border-radius: 5px;
    margin: 40px auto 40px;
    text-align: left;
  }

  .title {
    text-align: center;
    font-size: 24px;
    font-weight: bold;
    margin-bottom: 10px;
  }
  .description {
    text-align: left;
  }
  :global(.slider-container_map){
    position: absolute;
    display: flex;
    justify-content: center;
    left: 50%;
    margin-top: 20px;
    z-index: 999;
    transform: translateX(-45%);
  }
  #year-slider {
    margin-right: 10px;
  }
  :global(.tooltip) {
    position: absolute;
    background: #fff;
    border: 1px solid #ccc;
    padding: 10px;
    border-radius: 5px;
    color: black;
  }
  :global(.highlight) {
    stroke-width: 1.3;
    stroke: black;
  }
</style>
