<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
    
    let svg;
    let projection;
    let path;
    let worldData; // GeoJSON Data
    let covidData; // COVID-19 Data
    let dates; 
    let daysCount; 
    let tooltip;

    const colorScale = d3.scaleLinear()
            .domain([0, 10000, 50000, 100000, 500000, 1000000])
            .range(["#1FFB09", "#F9F208", "#F5BE16", "#F56016", "#F51616", "#A10707"]);

    onMount(async () => {
        worldData = await d3.json('globe.geo.json');
        const covidCsvData = await d3.csv('Covid_data/full_grouped.csv');
        covidData = processCovidData(covidCsvData);
        dates = Object.keys(covidData[Object.keys(covidData)[0]]);
        daysCount = dates.length - 1;
    
        // map projection
        projection = d3.geoMercator()
            .scale(100)
            .translate([svg.clientWidth / 2, svg.clientHeight / 2]);
    
        path = d3.geoPath().projection(projection);
        tooltip = d3.select('.tooltip');
        

        // Select the tooltip element using D3
        document.getElementById('timeSlider').max = daysCount;

        document.getElementById('timeSlider').addEventListener('input', function() {
            const currentDate = dates[this.value];
            document.getElementById('sliderLabel').textContent = `Date: ${currentDate}`;
            updateMap(currentDate);
        });
        
      drawMap();
      updateMap(dates[0]);
    });



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


    
    function drawMap() {
        const paths = d3.select(svg)
            .selectAll('path')
            .data(worldData.features)
            .enter()
            .append('path')
            .attr('d', path)
            .attr('fill', d => {
                const countryData = covidData[d.properties.name];
                const cases = countryData && countryData[dates[0]] ? countryData[dates[0]] : 0;
                return colorScale(cases);
            });

        // Attach event listeners
        paths.on('mouseover', (event, d) => {
                const countryData = covidData[d.properties.name];
                const cases = countryData && countryData[dates[0]] ? countryData[dates[0]] : '0';
                tooltip
                    .style('left', `${event.pageX + 15}px`)
                    .style('top', `${event.pageY + 15}px`)
                    .style('visibility', 'visible')
                    .html(`<strong>${d.properties.name}</strong><br/>Cases: ${cases}`);
            })
            .on('mousemove', (event) => {
                tooltip
                    .style('left', `${event.pageX + 15}px`)
                    .style('top', `${event.pageY + 15}px`);
            })
            .on('mouseout', () => {
                tooltip.style('visibility', 'hidden');
            });
    }

    function updateMap(currentDate) {
        const updateTooltip = (event, d) => {
            const countryData = covidData[d.properties.name];
            const cases = countryData && countryData[currentDate] ? countryData[currentDate] : 0;
            tooltip
                .style('visibility', 'visible')
                .html(`<strong>${d.properties.name}</strong><br/>Cases: ${cases}`);
        };
        d3.select(svg)
            .selectAll('path')
            .transition()
            .duration(500)
            .attr('fill', d => {
                const countryData = covidData[d.properties.name];
                const cases = countryData && countryData[currentDate] ? countryData[currentDate] : 0;
                return colorScale(cases);
            });

        // Update tooltip content without redefining event listeners
        d3.select(svg)
            .selectAll('path')
            .each(function(d) {
                const countryData = covidData[d.properties.name];
                const cases = countryData && countryData[currentDate] ? countryData[currentDate] : '0';
                d3.select(this)
                    .on('mouseover', (event) => {
                        tooltip
                            .style('left', `${event.pageX + 15}px`)
                            .style('top', `${event.pageY + 15}px`)
                            .style('visibility', 'visible')
                            .html(`<strong>${d.properties.name}</strong><br/>Cases: ${cases}`);
                    });
            });
    }

    </script>
  
    <svg bind:this={svg} width="100%" height="100%" viewBox="200 200 700 700"></svg>

    <div class="tooltip" bind:this={tooltip}></div>


  
  