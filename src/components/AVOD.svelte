<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import { annotation, annotationCalloutElbow } from 'd3-svg-annotation';

  let data = [];
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
    updateChart();
  }

  onMount(() => {
    fetchData();
  });

  function updateChart() {
    const stack = d3.stack()
      .keys(['basic', 'standard', 'premium', 'basic_with_ads'])
      .order(d3.stackOrderNone)
      .offset(d3.stackOffsetNone);

    const series = stack(data);

    const width = 1300, height = 600;
    const margin = { top: 30, right: 225, bottom: 25, left: 450 };
    const barWidth = width - margin.left - margin.right;
    const barHeight = height - margin.top - margin.bottom;
    
    const x = d3.scaleLinear()
      .domain([0, 100])
      .range([0, barWidth]);

    const y = d3.scaleBand()
      .domain(data.map(d => d.month))
      .range([0, barHeight])
      .padding(0.1);

    const color = d3.scaleOrdinal()
      .domain(['basic', 'standard', 'premium', 'basic_with_ads'])
      .range(['#4682B4', '#32CD32', '#FFD700', '#FF6347']); // Custom colors

    d3.select("#stacked-bar-chart").selectAll("*").remove();

    const svg = d3.select("#stacked-bar-chart")
      .attr("width", width)
      .attr("height", height)
      .append("g")
      .attr("transform", `translate(${margin.left}, ${margin.top})`);

    const tooltip = d3.select("body").append("div")
      .attr("class", "tooltip")
      .style("opacity", 0);

    svg.append("g")
      .selectAll("g")
      .data(series)
      .enter()
      .append("g")
      .attr("fill", d => color(d.key))
      .selectAll("rect")
      .data(d => d)
      .enter()
      .append("rect")
      .attr("y", d => y(d.data.month))
      .attr("x", d => x(d[0]))
      .attr("height", y.bandwidth())
      .attr("width", d => x(d[1]) - x(d[0]))
      .on("mouseover", function(event, d) {
        tooltip.transition()
          .duration(200)
          .style("opacity", .9);
        tooltip.html(`${d.data.month} - ${this.parentNode.__data__.key}: ${(d[1] - d[0]).toFixed(1)}%`)
          .style("left", (event.pageX + 5) + "px")
          .style("top", (event.pageY - 28) + "px");
      })
      .on("mouseout", function() {
        tooltip.transition()
          .duration(500)
          .style("opacity", 0);
      });

    svg.append("g")
      .attr("class", "x-axis")
      .attr("transform", `translate(0, ${barHeight})`)
      .call(d3.axisBottom(x).ticks(10).tickFormat(d => `${d}%`));

    svg.append("g")
      .attr("class", "y-axis")
      .call(d3.axisLeft(y));

    // Add legend
    const legend = svg.selectAll(".legend")
      .data(color.domain())
      .enter().append("g")
      .attr("class", "legend")
      .attr("transform", (d, i) => `translate(${barWidth + 20},${i * 20})`);

    legend.append("rect")
      .attr("x", 0)
      .attr("width", 18)
      .attr("height", 18)
      .style("fill", color);

    legend.append("text")
      .attr("x", 24)
      .attr("y", 9)
      .attr("dy", ".35em")
      .style("text-anchor", "start")
      .text(d => d);

    // Add annotations
    const annotations = [
      {
        note: {
          title: "Turning Point",
          label: "Netflix officially implemented its AVOD strategy by adding the 'Basic with Ads' plan at the lowest price. This move was more like a 'soft opening', allowing the public to gradually accept the new plan while retaining the existing ones.",
          wrap: 350,
          bgPadding: 10,
          padding: 15,
        },
        data: { month: 'Nov 2022' },
        dy: -30,
        dx: -50,
        color: ["#000"]
      },
      {
        note: {
          title: "Turning Point",
          label: "Netflix officially discontinued the 'Basic without Ads' plan. To maintain the same experience as before, subscribers now need to pay more for the 'Standard' plan. People who only want access need to watch ads.",
          wrap: 350,
          bgPadding: 10,
          padding: 15,
        },
        data: { month: 'Aug 2023' },
        dy: -20, // change text position
        dx: -60,
        color: ["#000"]
      }
    ];

    const makeAnnotations = annotation()
      .editMode(false)
      .notePadding(15)
      .type(annotationCalloutElbow)
      .accessors({
        x: d => 0, // annotation on the left hand of chart
        y: d => y(d.month) + y.bandwidth() / 2
      })
      .annotations(annotations);

    svg.append("g")
      .attr("class", "annotation-group")
      .call(makeAnnotations);
  }
</script>

<h1>Distribution of Sign-Ups by Plans at turning point from SVOD to AVOD</h1>

<div class="chart-container">
  <svg id="stacked-bar-chart"></svg>
</div>

<style>
  h1 {
    font-size: 20px;
    font-weight: bold;
    font-family: Arial, sans-serif;
    margin-top: 5px;
    margin-bottom: 20px;
  }

  .chart-container {
    display: block;
    margin: auto;
    position: relative;
    top: 35px; /* change chart position */
    /* z-index: 1;*/
  }

  #stacked-bar-chart {
    display: block;
    margin: auto;
    position: relative;
    margin-top: -40px;
  }

  .x-axis text,
  .y-axis text {
    font-family: sans-serif;
    font-size: 1.2em;
    fill: black;
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

  .legend rect {
    cursor: pointer;
  }

  .legend text {
    cursor: pointer;
  }
</style>
