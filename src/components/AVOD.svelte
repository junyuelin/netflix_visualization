<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
  
    let data = [];
    let selectedMonth = 'Aug 2022'; // Default month
    const originUrl = 'https://raw.githubusercontent.com/junyuelin/netflix_visualization/main/netflix%20static/SVOD%20vs%20AVOD/Transform%20into%20AVOD.csv';
  
    async function fetchData() {
      const response = await fetch(originUrl);
      const csvData = await response.text();
      data = d3.csvParse(csvData, d => ({
        month: d.Month,
        premium: +d.Premium || 0,
        standard: +d.Standard || 0,
        basic: +d.Basic || 0,
        basic_with_ads: +d["Basic with ads"] || 0
      }));
      updateChart(selectedMonth);
    }
  
    onMount(() => {
      fetchData();
    });
  
    function updateChart(month) {
      const monthData = data.find(d => d.month === month);
      if (!monthData) return;
  
      const pieData = [
        { label: 'Premium', percentage: monthData.premium },
        { label: 'Standard', percentage: monthData.standard },
        { label: 'Basic', percentage: monthData.basic },
        { label: 'Basic With Ads', percentage: monthData.basic_with_ads },
      ];
      const width = 800, height = 600;
      const radius = 200;
  
      const color = d3.scaleOrdinal()
        .domain(pieData.map(d => d.label))
        .range(['#FFD700', '#32CD32', '#4682B4', '#FF6347']); // Custom colors
  
      const pie = d3.pie()
        .value(d => d.percentage);
  
      const arc = d3.arc()
        .innerRadius(0)
        .outerRadius(radius);
  
      const outerArc = d3.arc()
        .innerRadius(radius * 0.8)
        .outerRadius(radius * 0.8);
  
      d3.select("#avod-pie-chart").selectAll("*").remove();
  
      const svg = d3.select("#avod-pie-chart")
        .attr("width", width)
        .attr("height", height)
        .append("g")
        .attr("transform", `translate(${width / 2}, ${height / 2})`);
  
      const arcs = svg.selectAll(".arc")
        .data(pie(pieData))
        .enter()
        .append("g")
        .attr("class", "arc");
  
      const tooltip = d3.select("body").append("div")
        .attr("class", "tooltip")
        .style("opacity", 0);
  
      arcs.append("path")
        .attr("d", arc)
        .attr("fill", d => color(d.data.label))
        .on("mouseover", function(event, d) {
          tooltip.transition()
            .duration(200)
            .style("opacity", .9);
          tooltip.html(`${d.data.label}: ${d.data.percentage.toFixed(1)}%`)
            .style("left", (event.pageX + 5) + "px")
            .style("top", (event.pageY - 28) + "px");
        })
        .on("mousemove", function(event) {
          tooltip.style("left", (event.pageX + 5) + "px")
            .style("top", (event.pageY - 28) + "px");
        })
        .on("mouseout", function(d) {
          tooltip.transition()
            .duration(500)
            .style("opacity", 0);
        });
  
      arcs.filter(d => d.data.percentage > 0).append("polyline")
        .attr("points", d => {
          const pos = outerArc.centroid(d);
          pos[0] = radius * 0.85 * (midAngle(d) < Math.PI ? 1 : -1);
          return [arc.centroid(d), outerArc.centroid(d), pos];
        })
        .attr("stroke", "black")
        .style("fill", "none");
  
      arcs.filter(d => d.data.percentage > 0).append("text")
        .attr("transform", d => {
          const pos = outerArc.centroid(d);
          pos[0] = radius * 0.85 * (midAngle(d) < Math.PI ? 1 : -1);
          return `translate(${pos})`;
        })
        .attr("dy", "0.35em")
        .attr("text-anchor", d => midAngle(d) < Math.PI ? "start" : "end")
        .text(d => `${d.data.label}: ${d.data.percentage.toFixed(1)}%`);
    }
  
    function midAngle(d) {
      return d.startAngle + (d.endAngle - d.startAngle) / 2;
    }
  
    function onMonthChange(event) {
      selectedMonth = data[+event.target.value].month;
      updateChart(selectedMonth);
    }
  </script>
  
  <h1>Distribution of Sign-Ups by Plans at turning point from SVOD to AVOD</h1>
  
  <div class="avod-chart-container">
    <svg id="avod-pie-chart"></svg> 
  </div>
  
  <div class="slider-container">
    <div class="month-display">{selectedMonth}</div>
    <input type="range" min="0" max="{data.length - 1}" value="{data.findIndex(d => d.month === selectedMonth)}" on:input="{onMonthChange}" />
    <span>{selectedMonth}</span>
  </div>
  
  <style>
    h1 {
      margin-top: 20px;
      margin-bottom: 0px;
    }
  
    .avod-chart-container {
      display: block;
      margin: auto;
      position: relative;
      top: 40px; /* Adjust this value to move the chart down */
      z-index: 1;
    }
  
    #avod-pie-chart {
      display: block;
      margin: auto;
      position: relative;
      margin-top: -40px;
    }
  
    .slider-container {
      text-align: center;
      margin: 20px 0 0px 0; /* Adjust this value to create space above the slider */
      position: relative;
      z-index: 2;
    }
  
    .month-display {
      font-size: 24px;
      text-align: center;
      margin-bottom: 10px;
    }
  
    .arc text {
      font-family: sans-serif;
      font-size: 1.2em;
    }
  
    .arc polyline {
      stroke: black;
      stroke-width: 1px;
      fill: none;
    }
  
    .tooltip {
      position: absolute;
      text-align: center;
      width: auto;
      height: auto;
      padding: 8px;
      background: lightsteelblue;
      border: 0px;
      border-radius: 8px;
      pointer-events: none;
    }
  </style>
  