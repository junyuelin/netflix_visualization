<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  let data;

  onMount(async () => {
    const rawData = await d3.csv('https://raw.githubusercontent.com/junyuelin/netflix_visualization/main/netflix%20static/subscriber%20count/netflix-subscribers-count-worldwide-2013-2024.csv');
    // Filter only Q2 data
    data = rawData.filter(d => d.quarter.startsWith('Q2'));
    drawChart();
  });

  function drawChart() {
    const margin = { top: 20, right: 30, bottom: 40, left: 40 };
    const width = 960 - margin.left - margin.right;
    const height = 500 - margin.top - margin.bottom;

    const svg = d3.select('#chart-container')
      .append('svg')
      .attr('width', width + margin.left + margin.right)
      .attr('height', height + margin.top + margin.bottom)
      .append('g')
      .attr('transform', `translate(${margin.left},${margin.top})`);

    const x = d3.scaleBand()
      .domain(data.map(d => d.quarter))
      .range([0, width])
      .padding(0.1);

    const y = d3.scaleLinear()
      .domain([0, d3.max(data, d => +d.subscriber)])
      .nice()
      .range([height, 0]);

    svg.append('g')
      .selectAll('.bar')
      .data(data)
      .enter().append('rect')
      .attr('class', 'bar')
      .attr('x', d => x(d.quarter))
      .attr('y', d => y(+d.subscriber))
      .attr('width', x.bandwidth())
      .attr('height', d => height - y(+d.subscriber));

    svg.append('g')
      .attr('class', 'x-axis')
      .attr('transform', `translate(0,${height})`)
      .call(d3.axisBottom(x))
      .selectAll('text')
      .attr('transform', 'rotate(-45)')
      .style('text-anchor', 'end');

    svg.append('g')
      .attr('class', 'y-axis')
      .call(d3.axisLeft(y));

    svg.append('text')
      .attr('class', 'axis-label')
      .attr('x', -height / 2)
      .attr('y', -margin.left + 15)
      .attr('transform', 'rotate(-90)')
      .style('text-anchor', 'middle')
      .text('Subscribers (in millions)');

    svg.append('text')
      .attr('class', 'axis-label')
      .attr('x', width / 2)
      .attr('y', height + margin.bottom)
      .style('text-anchor', 'middle')
      .text('Quarter');
  }
</script>

<style>
  :global(.bar) {
    fill: steelblue;
  }
  :global(.bar:hover) {
    fill: orange;
  }
  :global(.axis-label) {
    font: 12px sans-serif;
  }
  svg {
    font: 10px sans-serif;
  }
  #chart-container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 600px;
    width: 100%;
  }
</style>

<div id="chart-container"></div>
