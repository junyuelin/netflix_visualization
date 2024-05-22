<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  export let index;

  let data = [];
  let container;

  // URL of the CSV file
  const csvUrl = 'https://raw.githubusercontent.com/junyuelin/netflix_visualization/main/netflix%20static/revenue/netflix-annual-revenue-2002-2023.csv';

  // Load and process the data
  onMount(async () => {
    const response = await d3.csv(csvUrl, d => {
      return { year: +d.year, revenue: +d.revenue.replace(/"/g, '').replace(/,/g, '') };
    });
    data = response;
  });

  // Function to draw the chart
  function drawChart() {
    const margin = { top: 20, right: 30, bottom: 30, left: 40 };
    const width = 600 - margin.left - margin.right; // Adjust width to fit in the left half
    const height = 400 - margin.top - margin.bottom;

    // Clear previous content
    d3.select(container).selectAll('*').remove();

    const svg = d3.select(container)
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

  // Watch for changes in the index and container to conditionally draw the chart
  $: if (index === 2 && container) {
    drawChart();
  }
</script>

<div class="content" class:visible={index === 2}>
  <div class="chart-container" bind:this={container}>
    <!-- The chart will be drawn inside this div -->
  </div>
  <div class="text-container">
    <h2>Netflix Annual Revenue</h2>
    <p>
      This line chart represents Netflix's annual revenue from 2002 to 2023.
      As you can see, Netflix's revenue has been steadily increasing over the years,
      reflecting its growth and expansion in the streaming industry.
    </p>
    <!-- Add more descriptive text here as needed -->
  </div>
</div>

<style>
  .content {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
    opacity: 0;
    visibility: hidden;
    transition: opacity 0.5s ease-in-out;
  }
  .content.visible {
    opacity: 1;
    visibility: visible;
  }
  .chart-container {
    flex: 1;
    display: flex;
    justify-content: center;
    max-width: 50%;
  }
  .text-container {
    flex: 1;
    padding-left: 20px;
    padding-right: 100px;
    max-width: 25%;
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
