<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  export let index;
  // variable to track whether replay button is clicked
  let replayClicked = false;

  let data = [];
  let container1, container2; // Separate containers for each chart
  let textContainer, replayButton;

  // URL of the CSV file
  const csvUrl = 'https://raw.githubusercontent.com/junyuelin/netflix_visualization/main/netflix%20static/revenue/netflix-annual-revenue-2002-2023.csv';

  // Load and process the data
  onMount(async () => {
    await fetchData();

    // Hide charts when component mounts
    const hideButton = document.getElementById('hideButton');
    let chartsVisible = true;

    hideButton.addEventListener('click', () => {
      if (chartsVisible) {
        container1.style.display = 'none';
        container2.style.display = 'none';
        textContainer.style.display = 'none';
        chartsVisible = false;
        hideButton.innerText = 'Revenue Line Charts';
      } else {
        container1.style.display = 'block'; // Or 'flex', or whatever display value you want
        container2.style.display = 'block';
        textContainer.style.display = 'block';
        chartsVisible = true;
        hideButton.innerText = 'Subscriber Charts';
      }
    });
  });

  // Function to fetch and process data
  async function fetchData() {
    const response = await d3.csv(csvUrl, d => {
      return { year: +d.year, revenue: +d.revenue.replace(/"/g, '').replace(/,/g, '') };
    });
    data = response;
  }

  // Function to draw the chart
  function drawChart(container) {
    const margin = { top: 20, right: 30, bottom: 30, left: 40 };
    const width = 400 - margin.left - margin.right;
    const height = 300 - margin.top - margin.bottom;

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

    const gapSize = 5; // Adjust this value to control the size of the gap

    // Function to generate the path with gaps for dots
    const generatePathWithGaps = () => {
      let path = "";
      for (let i = 0; i < data.length - 1; i++) {
        const x1 = x(data[i].year);
        const y1 = y(data[i].revenue);
        const x2 = x(data[i + 1].year);
        const y2 = y(data[i + 1].revenue);

        const angle = Math.atan2(y2 - y1, x2 - x1);
        const offsetX = Math.cos(angle) * gapSize;
        const offsetY = Math.sin(angle) * gapSize;

        if (i === 0) {
          path += `M${x1 + offsetX},${y1 + offsetY}`;
        } else {
          path += `L${x1 + offsetX},${y1 + offsetY}`;
        }
        path += `L${x2 - offsetX},${y2 - offsetY}`;
      }
      return path;
    };

    // Add the x-axis
    svg.append('g')
      .attr('class', 'x-axis')
      .attr('transform', `translate(0,${height})`)
      .call(d3.axisBottom(x).ticks(data.length).tickFormat(d3.format('d')))
      .selectAll('text') // Select all text elements
      .attr('transform', 'rotate(-45) translate(-10, 0)') // Rotate and translate the labels
      .style('fill', 'black'); // Set color for axis labels

    // Add the y-axis
    svg.append('g')
      .attr('class', 'y-axis')
      .call(d3.axisLeft(y));

    // Create a group for the line
    const lineGroup = svg.append('g').attr('class', 'line-group');

    // Create the line path element with gaps
    lineGroup.append('path')
      .attr('class', 'line')
      .attr('fill', 'none')
      .attr('stroke', 'steelblue')
      .attr('stroke-width', 1.5)
      .attr('d', generatePathWithGaps());

    // Create a group for the circles
    const dotGroup = svg.append('g').attr('class', 'dot-group');

    // Create the circles for data points
    dotGroup.selectAll("circle")
      .data(data)
      .enter().append("circle")
      .attr("cx", d => x(d.year))
      .attr("cy", d => y(d.revenue))
      .attr("r", 4) // Set initial radius
      .attr("fill", "steelblue")
      .on("mouseover", function (event, d) {
        d3.select(this)
          .attr("r", 8) // Increase radius on hover
        tooltip
          .style("display", "block")
          .html(`Year: ${d.year}<br>Revenue: $${d.revenue.toLocaleString()}`)
          .style("left", `${event.pageX + 5}px`)
          .style("top", `${event.pageY - 28}px`);
      })
      .on("mouseout", function () {
        d3.select(this)
          .attr("r", 4) // Restore radius on mouseout
          .attr("fill", "steelblue"); // Restore color on mouseout
        tooltip.style("display", "none");
      });

    // Add the tooltip element
    const tooltip = d3.select("body").append("div")
      .attr("class", "tooltip")
      .style("position", "absolute")
      .style("background", "rgba(0, 0, 0, 0.7)")
      .style("color", "white")
      .style("padding", "5px 10px")
      .style("border-radius", "4px")
      .style("display", "none")
      .style("pointer-events", "none");

    // Animation for the line path
    const path = lineGroup.select('path');
    const totalLength = path.node().getTotalLength();
    
    const revenueText = svg.append('text')
      .attr('class', 'revenue-text')
      .attr('x', 30)
      .attr('y', 100)
      .attr('dy', '.35em')
      .text('');

    path
      .attr('stroke-dasharray', `${totalLength} ${totalLength}`)
      .attr('stroke-dashoffset', totalLength)
      .transition()
      .duration(3000)
      .ease(d3.easeLinear)
      .attr('stroke-dashoffset', 0)
      .on('start', function () {
        d3.active(this)
          .tween('text', function () {
            const interpolator = d3.interpolateNumber(0, data.length - 1);
            return function (t) {
              const index = Math.floor(interpolator(t));
              const currentData = data[index];
              revenueText.text(`$${currentData.revenue.toLocaleString()} millions in ${currentData.year}`);
            };
          });
      });

  }

  function drawGrowthRateChart(container) {
      const margin = { top: 20, right: 30, bottom: 30, left: 40 };
      const width = 400 - margin.left - margin.right;
      const height = 300 - margin.top - margin.bottom;

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

      // Calculate growth rate
      const growthRates = [NaN]; // Push NaN for the first growth rate
      for (let i = 1; i < data.length; i++) {
          const growthRate = ((data[i].revenue - data[i - 1].revenue) / data[i - 1].revenue);
          growthRates.push(growthRate);
      }

      const y = d3.scaleLinear()
          .domain([d3.min(growthRates), d3.max(growthRates)])
          .nice()
          .range([height, 0]);

      const gapSize = 5; // Adjust this value to control the size of the gap

      // Function to generate the path with gaps for dots
      const generatePathWithGaps = () => {
          let path = "";
          for (let i = 1; i < data.length - 1; i++) {
              const x1 = x(data[i].year);
              const y1 = y(growthRates[i]);
              const x2 = x(data[i + 1].year);
              const y2 = y(growthRates[i + 1]);

              const angle = Math.atan2(y2 - y1, x2 - x1);
              const offsetX = Math.cos(angle) * gapSize;
              const offsetY = Math.sin(angle) * gapSize;

              if (i === 1) {
                  path += `M${x1 + offsetX},${y1 + offsetY}`;
              } else {
                  path += `L${x1 + offsetX},${y1 + offsetY}`;
              }
              path += `L${x2 - offsetX},${y2 - offsetY}`;
          }
          return path;
      };

      // Add the x-axis
      svg.append('g')
        .attr('class', 'x-axis')
        .attr('transform', `translate(0,${height})`)
        .call(d3.axisBottom(x).ticks(data.length).tickFormat(d3.format('d')))
        .selectAll('text') // Select all text elements
        .attr('transform', 'rotate(-45) translate(-10, 0)') // Rotate and translate the labels
        .style('fill', 'black'); // Set color for axis labels

      // Add the y-axis
      svg.append('g')
          .attr('class', 'y-axis')
          .call(d3.axisLeft(y));

      // Create a group for the line
      const lineGroup = svg.append('g').attr('class', 'line-group');

      // Create the line path element with gaps
      lineGroup.append('path')
          .attr('class', 'line')
          .attr('fill', 'none')
          .attr('stroke', 'steelblue')
          .attr('stroke-width', 1.5)
          .attr('d', generatePathWithGaps());

      // Create a group for the circles
      const dotGroup = svg.append('g').attr('class', 'dot-group');
      // Create the circles for data points
      dotGroup.selectAll("circle")
          .data(data.slice(1)) // Exclude the first element
          .enter().append("circle")
          .attr("cx", (d, i) => x(data[i + 1].year)) // Use index + 1 to match with the sliced data
          .attr("cy", (d, i) => y(growthRates[i + 1])) // Use index + 1 to match with the sliced data
          .attr("r", 4) // Set initial radius
          .attr("fill", "steelblue")
          .on("mouseover", function (event, d) {
              d3.select(this)
                  .attr("r", 8); // Increase radius on hover
              tooltip
                  .style("display", "block")
                  .html(`Year: ${d.year}<br>Growth Rate: ${(growthRates[data.indexOf(d)] * 100).toFixed(2)}%`)
                  .style("left", `${event.pageX + 5}px`)
                  .style("top", `${event.pageY - 28}px`);
          })
          .on("mouseout", function () {
              d3.select(this)
                  .attr("r", 4); // Restore radius on mouseout
              tooltip.style("display", "none");
          });

      // Add the tooltip element
      const tooltip = d3.select("body").append("div")
          .attr("class", "tooltip")
          .style("position", "absolute")
          .style("background", "rgba(0, 0, 0, 0.7)")
          .style("color", "white")
          .style("padding", "5px 10px")
          .style("border-radius", "4px")
          .style("display", "none")
          .style("pointer-events", "none");

      // Animation for the line path
      const path = lineGroup.select('path');
      const totalLength = path.node().getTotalLength();

      path
          .attr('stroke-dasharray', `${totalLength} ${totalLength}`)
          .attr('stroke-dashoffset', totalLength)
          .transition()
          .duration(3000)
          .ease(d3.easeLinear)
          .attr('stroke-dashoffset', 0);
  }

  // Function to replay the chart
  function replayChart() {
    replayClicked = true;
  }

  // Watch for changes in the index and container to conditionally draw the chart
  $: if ((index === 1 || index === 2) && container1 && container2) {
    if (replayClicked) {
      fetchData().then(() => {
        drawChart(container1);
        drawGrowthRateChart(container2); // Call function to draw the growth rate chart
        replayClicked = false;
      });
    } else {
      drawChart(container1);
      drawGrowthRateChart(container2); // Call function to draw the growth rate chart
    }
  }

</script>

<div class="content" class:visible={index === 1 || index === 2}>
  <svg class="replay-button" width="40" height="40" viewBox="0 0 40 40" fill="none" xmlns="http://www.w3.org/2000/svg" bind:this={replayButton} on:click={replayChart}>
    <circle cx="20" cy="20" r="18" fill="white" stroke="black" stroke-width="2"/>
    <path d="M18 12L24 20L18 28V12Z" fill="black"/>
  </svg>

  <div class="charts-container">
    <div class="chart-container" bind:this={container1}></div>
    <div class="chart-container" bind:this={container2}></div>
  </div>
  <div class="text-container" bind:this={textContainer}>
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
    flex-direction: column;
    align-items: center;
    opacity: 0;
    visibility: hidden;
    transition: opacity 0.5s ease-in-out;
  }
  .content.visible {
    opacity: 1;
    visibility: visible;
  }

  .charts-container {
    display: flex;
    justify-content: center;
    margin-bottom: 20px;
  }
  .chart-container {
    max-width: 80%;
  }
  .text-container {
    padding-top: 20px;
    max-width: 80%;
    text-align: center;
  }

  :global(.line) {
    fill: none;
    stroke: steelblue;
    stroke-width: 3;
  }

  :global(.dot-group circle) {
    fill: orange;
    stroke: white;
    stroke-width: 2;
  }

  :global(.revenue-text) {
    font-size: 20px; /* Adjust the font size */
    fill: black; /* Text color */
  }

  :global(.tooltip) {
    position: absolute;
    background: rgba(0, 0, 0, 0.7);
    color: white;
    padding: 5px 10px;
    border-radius: 4px;
    font-size: 14px;
    pointer-events: none;
    transform: translate(-50%, -100%); /* Center the tooltip horizontally, move it above the cursor */
    transition: transform 0.1s ease-out;
    z-index: 999; /* Ensure the tooltip appears above other elements */
  }
</style>
