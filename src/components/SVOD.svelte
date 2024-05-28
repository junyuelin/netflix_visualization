<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
  
    let data = [];
  
    // URL of the CSV file
    const originUrl = 'https://raw.githubusercontent.com/junyuelin/netflix_visualization/main/netflix%20static/revenue/dvd-streaming-revenue-cleaned.csv';
  
    async function fetchData() {
        const response = await fetch(originUrl);
        const csvData = await response.text();
        // Parse CSV data
        data = csvData.split('\n').map(row => {
            const columns = row.split(',');
            return {
                year: parseInt(columns[0]),
                dvd: parseFloat(columns[1]),
                streaming: parseFloat(columns[2])
            };
        });
  
        // Calculate the total for each year
        data.forEach(d => {
          d.total = d.dvd + d.streaming;
        });
  
        createChart();
    }
  
    function createChart() {
      // Set up dimensions
      const margin = { top: 20, right: 30, bottom: 30, left: 40 };
      const width = 600 - margin.left - margin.right;
      const height = 400 - margin.top - margin.bottom;
  
      // Create SVG
      const svg = d3.select('.chart')
        .append('svg')
        .attr('width', width + margin.left + margin.right)
        .attr('height', height + margin.top + margin.bottom)
        .append('g')
        .attr('transform', `translate(${margin.left},${margin.top})`);
  
      // Set up data stack
      const stackData = d3.stack().keys(['dvd', 'streaming'])(data);
  
      // Create scales
      const xScale = d3.scaleBand()
        .domain(data.map(d => d.year))
        .range([0, width])
        .paddingInner(0.1);
  
      const yScale = d3.scaleLinear()
        .domain([0, d3.max(stackData, d => d3.max(d, d => d[1]))])
        .nice()
        .range([height, 0]);
  
      // Draw stacked bars
      svg.selectAll('.bar')
        .data(stackData)
        .enter().append('g')
        .attr('fill', (d, i) => i === 0 ? 'steelblue' : 'orange')
        .selectAll('rect')
        .data(d => d)
        .enter().append('rect')
        .attr('x', d => xScale(d.data.year))
        .attr('y', d => yScale(d[1]))
        .attr('height', d => yScale(d[0]) - yScale(d[1]))
        .attr('width', xScale.bandwidth());
  
      // Add x-axis
      svg.append('g')
        .attr('transform', `translate(0,${height})`)
        .call(d3.axisBottom(xScale));
  
      // Add y-axis
      svg.append('g')
        .call(d3.axisLeft(yScale));
    }
  
    onMount(async () => {
        await fetchData();
    });
  </script>

  <div class="container">
    <div class="chart"></div>
    <p> A stacked area chart to show dvd revenue vs streaming revenue.</p>
  </div>
  
  <style>
    .container {
      width: 100%;
      max-width: 800px; /* Adjust as needed */
      margin: 0 auto;
    }
  
    .chart {
      margin-bottom: 20px; /* Add space between chart and paragraph */
    }
  
    .bar {
      stroke: none;
    }
  
    .bar rect:hover {
      opacity: 0.7;
    }
  </style>