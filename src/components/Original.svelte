<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
    import PopupQuestion from './PopupQuestion.svelte';

    let data = [];
    let showPopup = false;
    let movies = [];

    const originalContent = [
        "Stranger Things", "The Witcher", "Bridgerton", "Money Heist (La Casa de Papel)",
        "Outer Banks", "Sex Education", "The Umbrella Academy", "To All the Boys I've Loved Before (movie series)"
    ];

    const licensedContent = [
        "Friends", "The Office (US)", "Gossip Girl", "Breaking Bad",
        "Avatar: The Last Airbender", "Gilmore Girls", "The Vampire Diaries", "The Walking Dead"
    ];

    // Mix original and licensed content
    movies = originalContent.flatMap((orig, i) => [orig, licensedContent[i]]);

    // URL of the CSV file
    const originUrl = 'https://raw.githubusercontent.com/junyuelin/netflix_visualization/main/netflix%20static/content%20spending/content-assets-cleaned.csv';

    onMount(async () => {
        await fetchData();
        drawChart();

        // Always allow the popup to show on refresh
        sessionStorage.removeItem('popupShown');

        const target = document.getElementById('popup-target');
        const observer = new IntersectionObserver((entries) => {
            entries.forEach(entry => {
                if (entry.isIntersecting && !showPopup && !sessionStorage.getItem('popupSubmitted')) {
                    showPopup = true;
                    sessionStorage.setItem('popupShown', 'true'); // Mark as shown for this session
                }
            });
        }, {
            threshold: 1.0
        });

        if (target) {
            observer.observe(target);
        }

        return () => {
            if (target) {
                observer.unobserve(target);
            }
        };
    });

    async function fetchData() {
        const response = await fetch(originUrl);
        const csv = await response.text();
        const parsedData = d3.csvParse(csv, d => ({
            year: +d.Year,
            licensed: +d['Licensed Content'],
            produced: +d['Produced Content'],
            total: +d['Total Content Asset']
        }));
        data = parsedData;
    }

    function drawChart() {
        const margin = { top: 20, right: 20, bottom: 55, left: 60 };
        const width = 600 - margin.left - margin.right;
        const height = 400 - margin.top - margin.bottom;
        
        const svg = d3.select("#chart")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", `translate(${margin.left},${margin.top})`);

        const x = d3.scaleLinear()
        .domain(d3.extent(data, d => d.year))
        .range([0, width]);

        const y = d3.scaleLinear()
        .domain([0, d3.max(data, d => Math.max(d.licensed, d.produced, d.total))])
        .nice()
        .range([height, 0]);

        svg.append("g")
        .attr("transform", `translate(0,${height})`)
        .call(d3.axisBottom(x).ticks(data.length).tickFormat(d3.format("d")));

        svg.append("g")
        .call(d3.axisLeft(y));

        // X-axis label
        svg.append("text")
            .attr("text-anchor", "end")
            .attr("x", width / 2 + 40)
            .attr("y", height + margin.bottom - 15)
            .text("Time (year)");

        // Y-axis label
        svg.append("text")
            .attr("text-anchor", "end")
            .attr("transform", "rotate(-90)")
            .attr("x", -height / 2 + 85)
            .attr("y", -margin.left + 20)
            .text("Content Asset (in Billion)");

        // Tooltip
        const tooltip = d3.select("body").append("div")
            .attr("class", "tooltip")
            .style("position", "absolute")
            .style("background", "#333")
            .style("padding", "5px 10px")
            .style("border", "1px solid #333")
            .style("border-radius", "5px")
            .style("pointer-events", "none")
            .style("opacity", 0)
            .style("color", "#fff");

        function showTooltip(event, d, type) {
            const content = `<strong>Year:</strong> ${d.year}<br><strong>${type} Content Asset:</strong> ${d[type.toLowerCase()]} billion`;
            tooltip.html(content)
                .style("left", (event.pageX + 10) + "px")
                .style("top", (event.pageY - 28) + "px")
                .style("opacity", 1);
        }

        function hideTooltip() {
            tooltip.style("opacity", 0);
        }

        // Licensed Content Line and Dots
        const lineLicensed = d3.line()
        .x(d => x(d.year))
        .y(d => y(d.licensed));
        
        svg.append("path")
        .datum(data)
        .attr("fill", "none")
        .attr("stroke", "steelblue")
        .attr("stroke-width", 1.5)
        .attr("d", lineLicensed);

        svg.selectAll(".dotLicensed")
        .data(data)
        .enter().append("circle")
        .attr("class", "dotLicensed")
        .attr("cx", d => x(d.year))
        .attr("cy", d => y(d.licensed))
        .attr("r", 3)
        .attr("fill", "steelblue")
        .on("mouseover", (event, d) => showTooltip(event, d, 'Licensed'))
        .on("mouseout", hideTooltip);

        // Produced Content Line and Dots
        const lineProduced = d3.line()
        .x(d => x(d.year))
        .y(d => y(d.produced));
        
        svg.append("path")
        .datum(data)
        .attr("fill", "none")
        .attr("stroke", "green")
        .attr("stroke-width", 1.5)
        .attr("d", lineProduced);

        svg.selectAll(".dotProduced")
        .data(data)
        .enter().append("circle")
        .attr("class", "dotProduced")
        .attr("cx", d => x(d.year))
        .attr("cy", d => y(d.produced))
        .attr("r", 3)
        .attr("fill", "green")
        .on("mouseover", (event, d) => showTooltip(event, d, 'Produced'))
        .on("mouseout", hideTooltip);

        // Total Content Line and Dots
        const lineTotal = d3.line()
        .x(d => x(d.year))
        .y(d => y(d.total));
        
        svg.append("path")
        .datum(data)
        .attr("fill", "none")
        .attr("stroke", "brown")
        .attr("stroke-width", 1.5)
        .attr("d", lineTotal);

        svg.selectAll(".dotTotal")
        .data(data)
        .enter().append("circle")
        .attr("class", "dotTotal")
        .attr("cx", d => x(d.year))
        .attr("cy", d => y(d.total))
        .attr("r", 3)
        .attr("fill", "brown")
        .on("mouseover", (event, d) => showTooltip(event, d, 'Total'))
        .on("mouseout", hideTooltip);
    }

    function closePopup() {
        showPopup = false;
        sessionStorage.setItem('popupSubmitted', 'true'); // Mark as submitted for this session
    }

    function handleSubmit() {
        showPopup = false;
        sessionStorage.setItem('popupSubmitted', 'true'); // Mark as submitted for this session
    }
</script>

<style>
    #chart-container {
        display: flex;
        flex-direction: column; /* Ensure elements are stacked in a column layout */
        align-items: center; /* Center alignment */
        margin-top: 20px; /* Top margin */
    }

    .page-title {
        font-size: 20px;
        font-weight: bold;
        font-family: Arial, sans-serif
    }

    .chart-and-legend {
        display: flex; /* Use flexbox for row layout */
        align-items: flex-start; /* Align items to the top */
        margin-top: 25px; /* Increase the top margin to move the chart and legend down */
    }

    #chart {
        font-family: Arial, sans-serif;
    }

    .legend {
        display: flex;
        flex-direction: column;
        margin-left: 20px; /* Space between chart and legend */
    }

    .legend-item {
        display: flex;
        align-items: center;
        margin-bottom: 10px; /* Margin between legend items */
    }

    .legend-color {
        width: 20px;
        height: 20px;
        margin-right: 5px;
    }

    .tooltip {
        position: absolute;
        background: #333;
        padding: 5px 10px;
        border: 1px solid #333;
        border-radius: 5px;
        pointer-events: none;
        opacity: 0;
        color: #fff;
    }

    .chart-description {
        text-align: center;
        margin-top: 10px; /* Increased margin-top for more spacing */
        max-width: 1050px; /* Increased max-width for longer lines */
        font-family: Arial, sans-serif;
        line-height: 1.5;
    }

    .popup-target {
        border: 1px solid red;
        height: 20px; /* Adjust as needed */
    }
</style>

<!-- Container to wrap the chart and legend -->
<div id="chart-container">
    <!-- Page title -->
    <div class="page-title">Netflix's Strategic Focus: Growth Through Original Content</div>

    <div class="chart-and-legend">
        <!-- Chart container -->
        <div id="chart"></div>
        <!-- Legend container -->
        <div class="legend">
            <div class="legend-item">
                <div class="legend-color" style="background-color: steelblue;"></div>
                <div>Licensed Content Asset</div>
            </div>
            <div class="legend-item">
                <div class="legend-color" style="background-color: green;"></div>
                <div>Produced Content Asset</div>
            </div>
            <div class="legend-item">
                <div class="legend-color" style="background-color: brown;"></div>
                <div>Total Content Asset</div>
            </div>
        </div>
    </div>
    <!-- Description text -->
    <div class="chart-description">
        This line chart illustrates Netflix's content asset distribution from 2016 to 2023, highlighting a significant increase in Produced Content Assets, which likely represent original content. This consistent growth, in contrast to the stable trend of Licensed Content Assets, emphasizes Netflix's strategic shift towards prioritizing original content. Netflix's focus on original content as a crucial component of its growth and competitive strategy in the streaming industry. By investing in original content, Netflix not only differentiates itself from competitors but also enhances its content library, attracting and retaining subscribers with unique offerings.
    </div>

    <!-- This is the target element that we want to observe -->
    <div id="popup-target" class="popup-target">End of Section</div>
</div>

<PopupQuestion movies={movies} isVisible={showPopup} on:close={closePopup} on:submit={handleSubmit} />