## Portfolio

---

### Data Analysis 

[Rangiora township growth since 1840](/sample_page)

<style type="text/css">
			
    .label {
		font-family: Helvetica, sans-serif;
		font-size: 4px;
		fill: rgb(239,101,72);
		text-anchor: middle;
    }

    #d3div1 {
        width: 500px;
        float: right;
        padding-bottom: 50px;
    }

</style>

<div id="d3div1">
    <div class="container">
        <h6>Set decade</h6>
            <div class="row align-items-center">
            <div class="col-sm-2"><p id="value-step"></p></div>
            <div class="col-sm"><div id="slider-step"></div></div>
            </div>
    </div>
</div>

<script src="https://d3js.org/d3.v7.min.js"></script>
<script src="https://unpkg.com/d3-simple-slider"></script>
<script type="text/javascript">

    //Width and height
    const w = 400;
    const h = 350;

    const color = d3.scaleQuantize()
    .range([`#addd8e`,`#d9f0a3`,`#f7fcb9`,`#ffffe5`,`#fff7bc`,`#fee391`,`#fec44f`,`#fe9929`]);
    //Colors derived from ColorBrewer, by Cynthia Brewer, and included in https://github.com/d3/d3-scale-chromatic

    //Define path generator, using the geoMercator projection
    const projection =
            d3.geoMercator()
                .scale([1000])
                .center([188, -44.5]);

    const path = d3.geoPath(projection);    // same as d3.geopath().projection(projection)

    //Create SVG element
    const svg = d3.select("#d3div1")
                .append("svg")
                .attr("width", w)
                .attr("height", h);

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
            //Loop through once for each region land area data value
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
                if (value) {        //If value exists…
                    return color(value);
                } else {            //If value is undefined…
                    return "#ccc";
                }});

            //Create one label per region
            const regionLabels = svg.selectAll("text")
                .data(json.features)
                .enter()
                .append("text")
                .attr("class", "label")
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

    const decade = [1850, 1860, 1870, 1880, 1890, 1900];

    // Step
    const sliderStep = d3
        .sliderBottom()
        .min(d3.min(decade))
        .max(d3.max(decade))
        .width(300)
        .tickFormat(d3.format('d'))
        .ticks(6)
        .step(10)
        .default(1850)
        .on('onchange', val => {
            d3.select('p#value-step').text((val));
        });

    const gStep = d3
        .select('div#slider-step')
        .append('svg')
        .attr('width', 500)
        .attr('height', 100)
        .append('g')
        .attr('transform', 'translate(30,30)');

    gStep.call(sliderStep);

    d3.select('p#value-step').text(d3.format('d')(sliderStep.value()));


</script>

---
[Project 2 Title](/pdf/sample_presentation.pdf)
<img src="images/dummy_thumbnail.jpg?raw=true"/>

---
[Project 3 Title](http://example.com/)
<img src="images/dummy_thumbnail.jpg?raw=true"/>

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
