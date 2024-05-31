<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  let data = [];
  let selectedYear = 2002;  // Default year
  const originUrl = 'https://raw.githubusercontent.com/junyuelin/netflix_visualization/main/netflix%20static/revenue/dvd-streaming-revenue-cleaned.csv';

  async function fetchData() {
      const response = await fetch(originUrl);
      const csvData = await response.text();
      data = d3.csvParse(csvData, d => ({
          year: +d.year,
          dvd: +d["dvd(in million)"],
          streaming: +d.streaming
      }));
      updateChart(selectedYear);
  }

  onMount(() => {
      fetchData();
  });

  function updateChart(year) {
      const yearData = data.find(d => d.year === year);
      if (!yearData) return;

      const total = yearData.dvd + yearData.streaming;
      const pieData = [
          { label: 'DVD', value: yearData.dvd, percentage: (yearData.dvd / total) * 100 },
          { label: 'Streaming', value: yearData.streaming, percentage: (yearData.streaming / total) * 100 }
      ];

      const width = 700, height = 600, radius = 150

      const color = d3.scaleOrdinal()
          .domain(pieData.map(d => d.label))
          .range(['#1f77b4', '#ff7f0e']);

      const pie = d3.pie()
          .value(d => d.value);

      const arc = d3.arc()
          .innerRadius(0)
          .outerRadius(radius);

      const outerArc = d3.arc()
          .innerRadius(radius * 0.8)
          .outerRadius(radius * 0.8);

      d3.select("#pie-chart").selectAll("*").remove();

      const svg = d3.select("#pie-chart")
          .attr("width", width)
          .attr("height", height)
          .append("g")
          .attr("transform", `translate(${width / 2}, ${height / 2})`);

      const arcs = svg.selectAll(".arc")
          .data(pie(pieData))
          .enter()
          .append("g")
          .attr("class", "arc");

      arcs.append("path")
          .attr("d", arc)
          .attr("fill", d => color(d.data.label));

      arcs.append("polyline")
          .attr("points", d => {
              const pos = outerArc.centroid(d);
              pos[0] = radius * 0.9 * (midAngle(d) < Math.PI ? 1 : -1);
              return [arc.centroid(d), outerArc.centroid(d), pos];
          })
          .attr("stroke", "black")
          .style("fill", "none");

      arcs.append("text")
          .attr("transform", d => {
              const pos = outerArc.centroid(d);
              pos[0] = radius * 0.9 * (midAngle(d) < Math.PI ? 1 : -1);
              return `translate(${pos})`;
          })
          .attr("dy", "0.35em")
          .attr("text-anchor", d => midAngle(d) < Math.PI ? "start" : "end")
          .text(d => `${d.data.label}: ${d.data.percentage.toFixed(1)}%`);
  }

  function midAngle(d) {
      return d.startAngle + (d.endAngle - d.startAngle) / 2;
  }

  function onYearChange(event) {
      selectedYear = +event.target.value;
      updateChart(selectedYear);
  }
</script>

<h1> Netflix's Major Events Timeline</h1>
<svg id="pie-chart"></svg> 
<div class="slider-container">
  <div class="year-display">{selectedYear}</div>
  <input type="range" min="2002" max="2023" bind:value={selectedYear} on:input={onYearChange} />
  <span>{selectedYear}</span>
</div>


<style>

  h1 {
        margin-top: 40px;
        margin-bottom: 0px; /* Add more space between the heading and the timeline */
      }

  #pie-chart {
    display: block;
    margin: auto; /* Center the pie chart horizontally */
    position: relative; /* Set position to relative */
    top: -80px; /* Adjust the vertical distance from the top */
  }

  .slider-container {
      text-align: center;
      margin: 0px 0;
  }

  .year-display {
      font-size: 24px;
      text-align: center;
      margin-bottom: 10px;
  }

  .arc text {
      font-family: sans-serif;
      font-size: 12px;
  }

  .arc polyline {
      stroke: black;
      stroke-width: 1px;
      fill: none;
  }
</style>

