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
      const covidCsvData = await d3.csv('Covid_data/worldometer_data.csv');
      covidData = processCovidData(covidCsvData);
  
      // Define the map projection
      projection = d3.geoMercator()
        .scale(100)
        .translate([svg.clientWidth / 2, svg.clientHeight / 2]);
  
      // Define the path generator
      path = d3.geoPath().projection(projection);
  
      // Draw the map
      drawMap(); 
    });
  
    // This function should process your CSV data and prepare it for mapping
    function processCovidData(csvData) {
      // Process your CSV data here to match it with the GeoJSON
      // For example, you might aggregate the cases by country
      // and create a new object keyed by country name or code
      let processedData = {};
      csvData.forEach(d => {
        // Assuming 'country' is a column in your CSV
        processedData[d.country] = d;
      });
      return processedData;
    }
  
    // This function draws the map with the countries colored by COVID-19 cases
    function drawMap() {
      d3.select(svg)
        .selectAll('path')
        .data(worldData.features)
        .enter()
        .append('path')
        .attr('d', path)
        .attr('fill', d => {
          // Get the COVID data for the current feature's country
          const countryData = covidData[d.properties.name];
          // Color the country based on the number of cases or some other metric
          return countryData ? colorScale(countryData.cases) : '#ccc';
        })
        .attr('stroke', 'white');
    }
  
    // Define a color scale for the number of cases
    function colorScale(cases) {
      // Define your scale here based on the number of cases
      // For simplicity, this is a placeholder function
      if (cases > 100000) {
        return 'red';
      } else if (cases > 50000) {
        return 'orange';
      } else {
        return 'green';
      }
    }
  </script>
  
  <svg bind:this={svg} width="800" height="600"></svg>
  
  