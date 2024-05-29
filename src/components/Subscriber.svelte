<script>
  import { onMount } from 'svelte';
  import { rawData } from "../dataset.js";
  import * as d3 from 'd3';
  export let index;

  let data;
  let diffData;

  console.log(rawData);

  onMount(() => {
    // Filter only Q2 data
    data = rawData; //.filter(d => d.quarter.startsWith('Q2'));

    // Calculate differences
    diffData = data.map((d, i, arr) => {
      if (i === 0) return { ...d, difference: 0 }; // No difference for the first data point
      return { ...d, difference: (d.subscriber - arr[i - 1].subscriber).toFixed(2) };
    });

    drawChart();
    drawDifferenceChart();
  });

  function drawChart() {
    const margin = { top: 20, right: 30, bottom: 40, left: 40 };
    const width = 660 - margin.left - margin.right;
    const height = 300 - margin.top - margin.bottom;

    const container = d3.select('#chart-container');

    const svg = container
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

    const tooltip = container.append('div')
      .attr('id', 'tooltip')
      .attr('class', 'tooltip')
      .style('visibility', 'hidden');

    svg.append('g')
      .selectAll('.bar')
      .data(data)
      .enter().append('rect')
      .attr('class', 'bar')
      .attr('x', d => x(d.quarter))
      .attr('y', height) // Start the bars at the bottom of the chart
      .attr('width', x.bandwidth())
      .attr('height', 0) // Start with zero height
      .on('mouseover', (event, d) => {
        tooltip.style('visibility', 'visible')
          .html(`Quarter: ${d.quarter}<br>Subscribers: ${d.subscriber} million`);
      })
      .on('mousemove', event => {
        const containerRect = container.node().getBoundingClientRect();
        tooltip.style('top', (event.clientY - containerRect.top - 10) + 'px')
          .style('left', (event.clientX - containerRect.left + 10) + 'px');
      })
      .on('mouseout', () => {
        tooltip.style('visibility', 'hidden');
      })
      .transition()
      .delay((d, i) => i * 200) // Delay each bar
      .duration(200) // Animation duration
      .attr('y', d => y(+d.subscriber))
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
      .attr('y', height + margin.bottom + 10)
      .style('text-anchor', 'middle')
      .text('Quarter');
  }

  function drawDifferenceChart() {
    const margin = { top: 20, right: 30, bottom: 40, left: 40 };
    const width = 660 - margin.left - margin.right;
    const height = 300 - margin.top - margin.bottom;

    const container = d3.select('#diff-chart-container');

    const svg = container
      .append('svg')
      .attr('width', width + margin.left + margin.right)
      .attr('height', height + margin.top + margin.bottom)
      .append('g')
      .attr('transform', `translate(${margin.left},${margin.top})`);

    const x = d3.scaleBand()
      .domain(diffData.map(d => d.quarter))
      .range([0, width])
      .padding(0.1);

    const y = d3.scaleLinear()
      .domain([d3.min(diffData, d => +d.difference), d3.max(diffData, d => +d.difference)])
      .nice()
      .range([height, 0]);

    const tooltip = container.append('div')
      .attr('id', 'tooltip')
      .attr('class', 'tooltip')
      .style('visibility', 'hidden');

    svg.append('g')
      .selectAll('.bar')
      .data(diffData)
      .enter().append('rect')
      .attr('class', 'bar')
      .attr('x', d => x(d.quarter))
      .attr('y', height) // Start the bars at the bottom of the chart
      .attr('width', x.bandwidth())
      .attr('height', 0) // Start with zero height
      .on('mouseover', (event, d) => {
        tooltip.style('visibility', 'visible')
          .html(`Quarter: ${d.quarter}<br>Difference: ${d.difference} million`);
      })
      .on('mousemove', event => {
        const containerRect = container.node().getBoundingClientRect();
        tooltip.style('top', (event.clientY - containerRect.top - 10) + 'px')
          .style('left', (event.clientX - containerRect.left + 10) + 'px');
      })
      .on('mouseout', () => {
        tooltip.style('visibility', 'hidden');
      })
      .transition()
      .delay((d, i) => i * 200) // Delay each bar
      .duration(200) // Animation duration
      .attr('y', d => d.difference >= 0 ? y(d.difference) : y(0))
      .attr('height', d => Math.abs(y(d.difference) - y(0)));

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
      .text('Difference in Subscribers (in millions)');

    svg.append('text')
      .attr('class', 'axis-label')
      .attr('x', width / 2)
      .attr('y', height + margin.bottom + 10)
      .style('text-anchor', 'middle')
      .text('Quarter');
  }
</script>

<div id="chart-container" style="position: relative;"></div>
<div id="diff-chart-container" style="position: relative; margin-top: 50px;"></div>

<style>
  :global(.bar) {
    fill: rgb(255, 102, 102);
  }
  :global(.bar:hover) {
    fill: pink;
  }
  :global(.axis-label) {
    font: 12px sans-serif;
  }
  svg {
    font: 10px sans-serif;
  }
  #chart-container, #diff-chart-container {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    position: relative;
  }
  .tooltip {
    position: absolute;
    visibility: hidden;
    background: #fff;
    border: 1px solid #ccc;
    padding: 5px;
    border-radius: 5px;
  }
</style>
