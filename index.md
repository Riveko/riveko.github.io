## Portfolio

---

### Data Analysis 

[Rangiora township growth since 1840](/sample_page)

<style type="text/css">
			
    .regionlabel {
		font-family: Helvetica, sans-serif;
		font-size: 4px;
		fill: rgb(239,101,72);
		text-anchor: middle;
    }

</style>

<div id="d3div1">
    <div class="container">
        <h6>Set decade</h6>
        <div class="row align-items-center">
            <div class="col-sm">
                <div id="slider-step"></div>
            </div>
        </div>
    </div>
</div>

<script src="https://d3js.org/d3.v7.min.js"></script>
<script src="https://unpkg.com/d3-simple-slider"></script>
<script type="text/javascript">

    const decade = [1850, 1860, 1870, 1880, 1890, 1900, 1910, 1920, 1930, 1940, 1950, 1960, 1970, 1980, 1990, 2000, 2010, 2020];
    let decadeValue = 1850;

    //Map width and height
    const w = 400;
    const h = 350;

    const color = d3
        .scaleQuantize()
        .range([`#004529`,`#006837`,`#238443`,`#41ab5d`,`#78c679`,`#addd8e`,`#d9f0a3`,`#f7fcb9`,`#ffffe5`,`#fff7bc`,`#fee391`,`#fec44f`,`#fe9929`,`#ec7014`,`#cc4c02`,`#993404`]);
        //Colors derived from ColorBrewer, by Cynthia Brewer

    //Define path generator, using the geoMercator projection
    const projection = d3
        .geoMercator()
        .scale([1000])
        .center([188, -44.5]);

    const path = d3.geoPath(projection);    // same as d3.geopath().projection(projection)

    //Create SVG element
    const svg = d3
        .select("div#d3div1")
        .append("svg")
        .attr("width", w)
        .attr("height", h);

    // Step slider
    const sliderStep = d3
        .sliderBottom()
        .min(d3.min(decade))
        .max(d3.max(decade))
        .width(450)
        .fill(`#004529`)
        .tickFormat(d3.format('d'))
        .ticks(18)
        .step(10)
        .default(decadeValue)
        .on(`onchange`, function(d) {
            decadeValue = d;
            redraw();
        });

        const gStep = d3
        .select('div#slider-step')
        .append('svg')
        .attr('width', 500)
        .attr('height', 100)
        .append('g')
        .attr('transform', 'translate(30,30)');

        gStep.call(sliderStep);

    //Load in region data
    d3.csv("/data/nz_region_decades.csv").then(data => {

        //Set input domain for color scale
        color.domain([
            d3.min(data, function(d) { return +d.value; }), 
            d3.max(data, function(d) { return +d.value; })
        ]);

        //Load in GeoJSON data
        d3.json("/data/nz_regions_simplified_geojson.json").then(json => {

            const formatDecimals = d3.format(",.2f");

            //Merge the region data and GeoJSON
            //Loop through once for each region
            for (i = 0; i < data.length; i++) {
        
                //Grab Region name
                const dataRegion = data[i].region;
                
                //Grab data value, and convert from string to float
                const dataValue = parseFloat(data[i].value);
        
                //Find the corresponding Region inside the GeoJSON
                for (j = 0; j < json.features.length; j++) {
                
                    const jsonRegion = json.features[j].properties.name;
        
                    if (dataRegion == jsonRegion) {
                
                        //Copy the data value into the JSON
                        json.features[j].properties.value = dataValue;
                        
                        //Stop looking through the JSON
                        break;
                        
                    }
                }		
            }
            
            //Bind data and create one path per GeoJSON feature
            const nzmap = svg.selectAll("path")
            .data(json.features)
            .enter()
            .append("path")
            .attr("d", path)                   
            .attr("fill", function(d) {
                //Get data value
                const value = d.properties.value;
                if (value && value <= decadeValue) {					
                    return color(value);
                } else {            // If Region decade does not exist or is > input decade
                    return "#004529";
                }});

            //Create one label per region
            const regionLabels = svg.selectAll("text")
                .data(json.features)
                .enter()
                .append("text")
                .attr("class", "regionlabel")
                .attr("x", function(d) { return path.centroid(d)[0]; })
                .attr("y", function(d) { return path.centroid(d)[1]; })
                .text(function(d) {
                    if (d.properties.value) {
                        return formatDecimals(d.properties.value);
                    }
            });

            //Load in cities data
            d3.csv("/data/nz-cities.csv").then(data => {
                
                const formatThousands = d3.format(",.2r");

                const cities = svg.selectAll("circle")
                    .data(data)
                    .enter()
                    .append("circle")
                    .attr("cx", function(d) {
                        return projection([d.lon, d.lat])[0];
                    })
                    .attr("cy", function(d) {
                        return projection([d.lon, d.lat])[1];
                    })
                    .attr("r", function(d) {
                        return Math.sqrt(parseInt(d.population) * 0.00003);
                    })
                    .attr("fill", "rgb(253,187,132)")
                    .attr("stroke", "gray")
                    .attr("stroke-width", 0.25)
                    .attr("opacity", 0.75)
                    .append("title")			//Simple tooltip
                    .text(function(d) {
                        return d.place + ": Pop. " + formatThousands(d.population);
                    });
                
            }).catch( err => {console.log(err)});

        }).catch( err => {console.log(err)});

    }).catch( err => {console.log(err)});
    
    function redraw() {

        // change map and chart to reflect selected decade
        svg.selectAll("path")
            .transition()
            .attr("fill", function(d,i) {
                //Get data value
                const value = d.properties.value;
                if (value && value <= decadeValue) {
                    return color(value);
                } else {            //If Region decade does not exist or is > input decade
                    return "#004529";
            }});
    }

</script>

---
[Project 2 Title](/pdf/sample_presentation.pdf)


---
[Project 3 Title](http://example.com/)


---

### Category Name 2

- [Project 1 Title](http://example.com/)
- [Project 2 Title](http://example.com/)
- [Project 3 Title](http://example.com/)
- [Project 4 Title](http://example.com/)
- [Project 5 Title](http://example.com/)

---




---
<p style="font-size:11px">Page template forked from <a href="https://github.com/evanca/quick-portfolio">evanca</a></p>
<!-- Remove above link if you don't want to attibute -->
