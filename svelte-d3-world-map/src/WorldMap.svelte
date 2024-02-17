<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
    
    let svg;
    let projection;
    let path;
    let worldData; // This will hold the loaded GeoJSON data
    let covidData; // This will hold the processed COVID-19 data
  
    onMount(async () => {
        // Load the GeoJSON data for the world map
        worldData = await d3.json('globe.geo.json');
    
        // Load and process the COVID-19 CSV data
        const covidCsvData = await d3.csv('Covid_data/full_grouped.csv');
        covidData = processCovidData(covidCsvData);
    
        // Define the map projection
        projection = d3.geoMercator()
            .scale(100)
            .translate([svg.clientWidth / 2, svg.clientHeight / 2]);
    
        // Define the path generator
        path = d3.geoPath().projection(projection);
        // Inside your onMount function, after loading data and setting up the map

        // Select the tooltip element using D3
        const tooltip = d3.select('.tooltip');
        const dates = Object.keys(covidData[Object.keys(covidData)[0]]); // Assuming all countries have the same dates
        const daysCount = dates.length - 1;

        document.getElementById('timeSlider').max = daysCount;

        document.getElementById('timeSlider').addEventListener('input', function() {
            const currentDate = dates[this.value];
            document.getElementById('sliderLabel').textContent = `Date: ${currentDate}`;
            updateMap(currentDate);
        });


  
      // Draw the map
      drawMap();
    });


    function updateMap(currentDate) {
        console.log('Current date:', currentDate);

        d3.select(svg)
            .selectAll('path')
            .attr('fill', d => {
                const countryData = covidData[d.properties.name];
                const cases = countryData ? countryData[currentDate] : 0;

                console.log(d.properties.name, cases, colorScale(cases)); // Log the country, cases, and color

                return colorScale(cases);
            });
    }





    function processCovidData(csvData) {
        let processedData = {};
        csvData.forEach(d => {
            const cases = parseInt(d.Confirmed) || 0;
            const date = d.Date;
            const country = d['Country/Region'];

            if (!processedData[country]) {
                processedData[country] = {};
            }

            processedData[country][date] = cases;
        });
        return processedData;
    }



    const colorScale = d3.scaleLinear()
        .domain([0, 10000, 50000, 100000, 500000, 1000000]) // Adjust based on your data
        .range(["#99F98E", "#yellowgreen", "#yellow", "#orange", "#red", "#darkred"]); // Start with light green

    function drawMap() {
        const tooltip = d3.select('.tooltip');

        d3.select(svg)
            .selectAll('path')
            .data(worldData.features)
            .enter()
            .append('path')
            .attr('d', path)
            .attr('fill', '#99F98E') // Set all countries to light green
            .on('mouseover', function(event, d) {
                tooltip.style('visibility', 'visible')
                    .text(d.properties.name);
            })
            .on('mousemove', function(event) {
                tooltip.style('left', `${event.pageX + 15}px`)
                    .style('top', `${event.pageY - 25}px`);
            });
    }
    
    </script>
  
    <svg bind:this={svg} width="100%" height="100%" viewBox="200 200 700 700"></svg>

    <div class="tooltip"></div>


  
  