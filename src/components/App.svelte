<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  let data = [];

  // URL of the CSV file
  const csvUrl = 'https://raw.githubusercontent.com/junyuelin/netflix_visualization/main/netflix%20static/revenue/netflix-annual-revenue-2002-2023.csv';

  // Load and process the data
  onMount(async () => {
    const response = await d3.csv(csvUrl, d => {
      return { year: +d.year, revenue: +d.revenue.replace(/"/g, '').replace(/,/g, '') };
    });
    data = response;
    drawChart();
  });

  // Function to draw the chart
  function drawChart() {
    const margin = { top: 20, right: 30, bottom: 30, left: 40 };
    const width = 800 - margin.left - margin.right;
    const height = 400 - margin.top - margin.bottom;

    const svg = d3.select('#chart')
      .append('svg')
      .attr('width', width + margin.left + margin.right)
      .attr('height', height + margin.top + margin.bottom)
      .append('g')
      .attr('transform', `translate(${margin.left},${margin.top})`);

    const x = d3.scaleLinear()
      .domain(d3.extent(data, d => d.year))
      .range([0, width]);

    const y = d3.scaleLinear()
      .domain([0, d3.max(data, d => d.revenue)])
      .nice()
      .range([height, 0]);

    const line = d3.line()
      .x(d => x(d.year))
      .y(d => y(d.revenue));

    // Add the x-axis
    svg.append('g')
      .attr('class', 'x-axis')
      .attr('transform', `translate(0,${height})`)
      .call(d3.axisBottom(x).ticks(data.length).tickFormat(d3.format('d')));

    // Add the y-axis
    svg.append('g')
      .attr('class', 'y-axis')
      .call(d3.axisLeft(y));

    // Create the line path element
    const path = svg.append('path')
      .datum(data)
      .attr('class', 'line')
      .attr('fill', 'none')
      .attr('stroke', 'steelblue')
      .attr('stroke-width', 1.5)
      .attr('d', line);

    // Animation for the line path
    const totalLength = path.node().getTotalLength();

    path
      .attr('stroke-dasharray', `${totalLength} ${totalLength}`)
      .attr('stroke-dashoffset', totalLength)
      .transition()
      .duration(2000)
      .ease(d3.easeLinear)
      .attr('stroke-dashoffset', 0);
  }
</script>

<main>
  <h1>Netflix Revenue Line Chart</h1>
  <div id="chart"></div>
</main>

<style>

  main {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  #chart {
    max-width: 100%;
  }

  .line {
    fill: none;
    stroke: steelblue;
    stroke-width: 2;
  }

  .axis path,
  .axis line {
    fill: none;
    shape-rendering: crispEdges;
  }
</style>
