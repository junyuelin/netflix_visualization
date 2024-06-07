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
  
        const width = 650, height = 350;  // Increased size
        const radius = 160;
  
        const color = d3.scaleOrdinal()
            .domain(pieData.map(d => d.label))
            .range(['#EC6B56', '#47B39C']);
  
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
        
        const tooltip = d3.select("body").append("div")
            .attr("class", "tooltip")
            .style("opacity", 0);
  
        arcs.append("path")
            .attr("d", arc)
            .attr("fill", d => color(d.data.label))
            .attr("stroke", "black")
            .attr("stroke-width", "1.5px")
            .on("mouseover", function(event, d) {
                const [x, y] = arc.centroid(d);
                d3.select(this)
                    .transition()
                    .duration(200)
                    .attr("transform", `translate(${x * 0.1}, ${y * 0.1})`);
                
                tooltip.transition()
                    .duration(200)
                    .style("opacity", .9);
                tooltip.html(`${d.data.label} revenue: $${d.data.value} million (${d.data.percentage.toFixed(1)}%)`)
                    .style("left", (event.pageX + 5) + "px")
                    .style("top", (event.pageY - 28) + "px");
            })
            .on("mousemove", function(event) {
                tooltip.style("left", (event.pageX + 5) + "px")
                    .style("top", (event.pageY - 28) + "px");
            })
            .on("mouseout", function(event, d) {
                d3.select(this)
                    .transition()
                    .duration(200)
                    .attr("transform", `translate(0, 0)`);
                
                tooltip.transition()
                    .duration(500)
                    .style("opacity", 0);
            });
  
        arcs.append("polyline")
            .attr("points", d => {
                const pos = outerArc.centroid(d);
                pos[0] = radius * 0.85 * (midAngle(d) < Math.PI ? 1 : -1);
                return [arc.centroid(d), outerArc.centroid(d), pos];
            })
            .attr("stroke", "black")
            .style("fill", "none");
  
        arcs.append("text")
            .attr("transform", d => {
                const pos = outerArc.centroid(d);
                pos[0] = radius * 0.95 * (midAngle(d) < Math.PI ? 1 : -1); // Adjusted position
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
  
<h1>Netflix's DVD Revenue vs Streaming Revenue over time</h1>
  
<svg id="pie-chart"></svg> 
  
<div class="slider-container">
    <input type="range" min="2002" max="2023" value={selectedYear} on:input={onYearChange} />
    <span>{selectedYear}</span>
</div>
<div class="description">
    <h2>From DVD-by-Mail Era to The Wind-down of DVD</h2>
    <ul>
        <li><span>2007:</span> Introduction of Netflix streaming service</li>
        <li><span>2010:</span> Netflix offering streaming services bundled with DVD</li>
        <li><span>2011:</span> Netflix' plan to separate its DVD and streaming services, which was abandoned due to customer backlash</li>
        <li><span>After 2011:</span> Continuous decline of DVD Revenue, and growing popularity of Streaming Services.</li>
    </ul>
</div>

<style>
  h1 {
    text-align: center;
    font-size: 2em;
  }
  
  .description {
    background-color: #f9f9f9;
    border: 1px solid #ccc;
    padding: 20px;
    margin-top: 20px;
    text-align: left; /* Changed to left-align the list content */
    border-radius: 8px;
    max-width: 800px;
    margin: 20px auto;
  }

  .description h2 {
    text-align: center;
    font-size: 1.5em;
    font-weight: bold;
    margin-top: -5px; /* Reduced space between top border and heading */
  }

  .description ul {
    list-style-type: disc;
    padding-left: 20px;
  }

  .description li {
    margin-bottom: 10px;
  }

  .description li span {
    font-weight: bold;
    margin-right: 10px;
  }

  #pie-chart {
    display: block;
    margin: 20px auto;
    position: relative;
    top: 0;
    z-index: 1;
  }

  .slider-container {
    text-align: center;
    position: relative;
    z-index: 2;
  }

  .year-display {
    font-size: 24px;
    text-align: center;
    margin-bottom: 10px;
  }

  .arc text {
    font-family: sans-serif;
    font-size: 1em;
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
