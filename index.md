## Portfolio

---

### Data Analysis 

[Rangiora township growth since 1840](/sample_page)

<style type="text/css">
			
    .propertylabel {
		font-family: Helvetica, sans-serif;
		font-size: 4px;
		fill: rgb(239,101,72);
		text-anchor: middle;
        display: none;
    }

    #divStoryBox {
        position: absolute; 
        width: 300px;
        height: auto;
        padding: 10px;
        background-color: white;
        -webkit-border-radius: 10px;
        -moz-border-radius: 10px;
        border-radius: 10px;
        -webkit-box-shadow: 4px 4px 10px rgba(0, 0, 0, 0.4);
        -moz-box-shadow: 4px 4px 10px rgba(0, 0, 0, 0.4);
        box-shadow: 4px 4px 10px rgba(0, 0, 0, 0.4);
        pointer-events: none;
    }
			
	#divStoryBox.hidden {
		display: none;
	}

    #divStoryBox p, #divChart p, #divMap p {
        margin: 0;
        font-family: sans-serif;
        font-size: 12px;
        line-height: 16px;    
    }

</style>

<div id="d3div">
    <!-- Step slider-->
    <div class="row align-items-center">
        <div class="col-sm">
            <div id="slider-step"></div>
        </div>
    </div>	
        <!-- Population bar chart -->
    <div id="divChart">
        <p><span style="font-size: 12px; color: #8d918d">Population</span></p>
    </div>
    <!-- Properties map -->
    <div id="divMap">
        <p><span style="font-size: 12px; color: #8d918d">Property growth</span></p>
    </div>
    <!-- Story box for text and images -->
    <div id="divStoryBox" >
        <p><strong><span id="storyBoxTitle"></span></strong></p>
        <p><span id="storyBoxText"></span></p>
        <img src="/images/rangiora_high_street.png" alt="Farmers Co-op store" height="90px" width="160px">
        <p><span id="storyBoxCitation" style="font-size: 9px; color: #8d918d"></span></p>
    </div>
</div>

<script src="https://d3js.org/d3.v7.min.js"></script>
<script src="https://unpkg.com/d3-simple-slider"></script>
<script type="text/javascript">

    //Define Global variables
    const baseDataset = [ 
        { key: 0, decade: 1840, population: 0, color: `#004529`, text: `Prior to Ngati Toa's attack and destruction of Kaiapohia Pa in 1831, Ngai Tahu built pataka in Rangiora to store weapons and food.` },		
        { key: 1, decade: 1850, population: 20, color: `#004529`, text: `In 1851, Charles Torlesse and John Boys, together with their wives (two Townsend sisters from Ferrymead - Alicia and Priscilla) were the first Europeans to build homes on recently acquired rural sections in Rangiora` },		
        { key: 2, decade: 1860, population: 200, color: `#006837`, text: `1860's text` },		
        { key: 3, decade: 1870, population: 750, color: `#238443`, text: `1870's text` },
        { key: 4, decade: 1880, population: 1500, color: `#41ab5d`, text: `1880's text` },
        { key: 5, decade: 1890, population: 1800, color: `#78c679`, text: `1890's text` },
        { key: 6, decade: 1900, population: 1800, color: `#addd8e`, text: `1900's text` },
        { key: 7, decade: 1910, population: 1800, color: `#d9f0a3`, text: `By the 1910's, Rangiora had still not grown much` },
        { key: 8, decade: 1920, population: 2000, color: `#f7fcb9`, text: `1920's text` },
        { key: 9, decade: 1930, population: 2100, color: `#ffffe5`, text: `1930's text` },
        { key: 10, decade: 1940, population: 2300, color: `#fff7bc`, text: `1940's text` },
        { key: 11, decade: 1950, population: 2800, color: `#fee391`, text: `1950's text` },
        { key: 12, decade: 1960, population: 3500, color: `#fec44f`, text: `1960's text` },
        { key: 13, decade: 1970, population: 4800, color: `#fe9929`, text: `1970's text` },
        { key: 14, decade: 1980, population: 6400, color: `#ec7014`, text: `1980's text` },
        { key: 15, decade: 1990, population: 8800, color: `#cc4c02`, text: `1990's text` },
        { key: 16, decade: 2000, population: 10800, color: `#e31a1c`, text: `2000's text` },
        { key: 17, decade: 2010, population: 12000, color: `#bd0026`, text: `2010's text` },
        { key: 18, decade: 2020, population: 23000, color: `#800026`, text: `Following the Christchurch earthquakes in 2010 and 2011, many "red stickered" residents used their Government and insurance pay-outs to build new homes in Rangiora subdivisions, recently developed on ex-farmland beyond the original town "belts".` }
    ];

    const decades = [];
    const colors = [];
    baseDataset.forEach(item => {
        decades.push(item.decade);
        colors.push(item.color);
    })

    let decadeValue = 2020;
    let dataset = baseDataset.slice(0,decades.indexOf(decadeValue)+1);
    let decadeValueDataObject = [baseDataset[decades.indexOf(decadeValue)]];
    const maxPopulation = d3.max(baseDataset, d => d.population);

    const w = 350;
    const h = 350;

    
    //Set up step slider control svg
    const sliderStep = d3
        .sliderBottom()
        .min(d3.min(decades))
        .max(d3.max(decades))
        .width(300)
        .fill(`#004529`)
        .tickFormat(d3.format('d'))
        .ticks(4)
        .step(10)
        .default(decadeValue)
        .on(`onchange`, function(d) {
            decadeValue = d;
            redraw();
        });

    const gStep = d3	
        .select(`div#slider-step`)
        .append(`svg`)
        .attr(`width`, 350)
        .attr(`height`, 80)
        .append(`g`)
        .attr(`transform`, `translate(30,30)`);

    //Create bar chart svg element, scales and axes
    const chartMargin = { top: 10, right: 10, bottom: 10, left: 40 };
    const chartWidth = 350 - chartMargin.left - chartMargin.right;
    const chartHeight = 60 - chartMargin.top - chartMargin.bottom;
    const barHeight = 20;

    const svgChart = d3
        .select(`div#divChart`)
        .append(`svg`)
        .attr(`width`, chartWidth + chartMargin.left + chartMargin.right)
        .attr(`height`, chartHeight + chartMargin.top + chartMargin.bottom)
        .append(`g`)
        .attr(`transform`, `translate(20,0)`);

    const xScale = d3.scaleLinear()
        .domain([0, maxPopulation])
        .range([0, chartWidth]);

    const yScale = d3.scaleBand()
        .domain([decadeValue])
        .rangeRound([0, chartHeight])
        .padding(0.15);

    const xAxis = d3.axisBottom(xScale).ticks(2, `,.3d`).tickSize(0);
    const yAxis = d3.axisLeft(yScale).tickValues([]);

    svgChart.append(`g`)
        .attr(`transform`, `translate(0,${chartHeight - 10})`)
        .call(xAxis)
        .call(g => g.select(`.domain`).remove());

    svgChart.append(`g`)
        .call(yAxis)
        .call(g => g.select(`.domain`).remove());

 /*   const key = (d) => d.key;

    //Define path generator, using the geoMercator projection
    const projection = d3
        .geoMercator()
        .scale([340000])
        .center([172.64, -43.317]);

    const path = d3.geoPath(projection);
    
    //Create map svg element
    const svgMap = d3
        .select("div#divMap")
        .append("svg")
        .attr("width", w)
        .attr("height", h);
             
    //Function - add bar to population bar chart svg
    function populationChart() {

        svgChart.selectAll(`rect`)
            .data(decadeValueDataObject)
            .enter()
            .append(`rect`)
            .attr(`x`, 0)
            .attr(`y`, d => yScale(d.decade))
            .attr(`height`, barHeight)
            .attr(`width`,d => ( d.population * chartWidth / maxPopulation ))
            .attr(`fill`, d => d.color);

    }
    
    //Function - set up properties map svg
    function propertyMap () {

        d3.json("/data/rangiora_property_titles.json").then(json => {				

            //Bind data and create one path per property
            const rangioramap = svgMap
                .selectAll("path")
                .data(json.features)
                .enter()
                .append("path")
                .attr("d", path)                   
                .attr("fill", function(d) {
                    //Get decade color for the property
                    const value = parseInt(d.properties.decade);
                    if (value && value <= decadeValue) {
                        return colors[decades.indexOf(value)];
                    } else {            // If property decade does not exist or is > input decade
                        return "#004529";
                    }
                });

            //Create one label per property
            const propertyLabels = svgMap
                .selectAll("text")
                .data(json.features)
                .enter()
                .append("text")
                .attr("class", "propertylabel")
                .attr("x", function(d) { return path.centroid(d)[0]; })
                .attr("y", function(d) { return path.centroid(d)[1]; })
                .text(function(d) {
                    if (d.properties.seqno) {
                        return d.properties.seqno;
                    }
                });

        }).catch( err => {console.log(err)});
        
    }
                
    //Function - set up story box svg
    function storyBox () {

        d3.select(`#divStoryBox`)
            .select(`#storyBoxTitle`)
            .text(decadeValue + `'s population: ` + dataset[decades.indexOf(decadeValue)].population);

        d3.select(`#divStoryBox`)
            .select(`#storyBoxText`)
            .text(dataset[decades.indexOf(decadeValue)].text);

        d3.select(`#divStoryBox`)
            .select(`#storyBoxCitation`)
            .text(decadeValue < 1980 ? `Source: Rangiora by D.N Hawkins, Rangiora Borough Council 1983` : ``);
            
        //Display the story box
        d3.select(`#divStoryBox`).classed(`hidden`, false);
        
    }
    
    //Function - redraw map svg and bar chart on change of decade in slider control
    function redraw() {

        const lastKeyValue = dataset.length - 1;
        if (decadeValue != decades[dataset.length - 1]) {
            dataset = baseDataset.slice(0,decades.indexOf(decadeValue)+1);
        }
        
        //Update chart to reflect selected decade    
        decadeValueDataObject = [baseDataset[decades.indexOf(decadeValue)]];
        yScale.domain([decadeValueDataObject.decade]);

        const bar = svgChart.selectAll("rect")
            .data(decadeValueDataObject);

       bar.enter()
            .append("rect")
            .attr(`x`, chartWidth)
            .attr(`y`, d => yScale(d.decade))
            .attr(`height`, barHeight)
            .attr(`width`,d => ( d.population * chartWidth / maxPopulation ))
            .attr(`fill`, d => d.color)
            .merge(bar)
            .transition()
            .duration(200)
            .attr(`x`, 0)
            .attr(`y`, d => yScale(d.decade))
            .attr(`height`, barHeight)
            .attr(`width`,d => ( d.population * chartWidth / maxPopulation ));
      
 /*       bar.exit()
            .transition()
            .duration(200)
            .attr("x", -xScale.bandwidth())
            .remove();
*/
        // change map to reflect selected decade
        svgMap.selectAll("path")
            .transition()
            .attr("fill", function(d,i) {
                //Get data value
                const value = parseInt(d.properties.decade);
                if (value && value <= decadeValue) {
                    return colors[decades.indexOf(value)];
                } else {            //If property decade does not exist or is > input decade
                    return "#004529";
            }});

        //Update the story text box
        d3.select(`#divStoryBox`)
            .select(`#storyBoxTitle`)
            .text(decadeValue + `'s population: ` + dataset[decades.indexOf(decadeValue)].population);

        d3.select(`#divStoryBox`)
            .select(`#storyBoxText`)
            .text(dataset[decades.indexOf(decadeValue)].text);

        d3.select(`#divStoryBox`)
            .select(`#storyBoxCitation`)
            .text(decadeValue < 1980 ? `Source: Rangiora by D.N Hawkins, Rangiora Borough Council 1983` : ``);
        
        //Display the story text box
        d3.select("#divStoryBox").classed("hidden", false);
            
    }			
    
    //Function - main function that runs each of the component functions
    function runInfographic () {
    
        gStep.call(sliderStep);	//Runs the slider step control

        populationChart();		//Sets up initial display of the population bar chart	
        
        propertyMap();			//Sets up initial display of the properties map
        
        storyBox();				//Sets up initial display of the story box
		
    }
    
    runInfographic();

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
