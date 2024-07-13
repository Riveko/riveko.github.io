## Portfolio

---

### Interactive Data Visualisation 

<style type="text/css">	
  
    #play-controls {
        text-align: left;
        min-width: 320px;
        max-width: 320px;
        margin: 0 auto;
        padding: 5px 0 1em;
    }

    #play-controls * {
        display: inline-block;
        vertical-align: middle;
    }

	#steplist {
		display: none;
	}

	#slider-control-values {
		width: 270px;
		margin: 0px 10px 0px 30px;
		font-size: 10px;
        font-weight: 600;
		color: #8d918d;
	}
	
	.align-left {
		float: left;
	}
	
	.align-right {
		float: right;
	}

    #play-pause-button {
        width: 20px;
        height: 20px;
        text-align: center;
        font-size: 15px;
        cursor: pointer;
        color: #222;
        border: 0;
        background: transparent;
    }

    #play-range {
        margin: 2.5%;
        width: 80%;
        accent-color: #004529;
    }

    #play-output {
        font-family: Arial, Helvetica, sans-serif;
    }

    input[type="range"]::-moz-range-track {
        padding: 0 10px;
        background: repeating-linear-gradient(to right, 
            #ccc, 
            #ccc 10%, 
            #000 10%, 
            #000 11%, 
            #ccc 11%, 
            #ccc 20%);
	}

    #d3div {
        border-radius: 25px;
        background: #e4dbcd;
        padding: 10px; 
        width: 320px;
        height: auto;  
    }

    #d3div h4 a {
        margin: 10px 10px 10px 35px;
        font-family: sans-serif;
        font-size: 14px;
        font-weight: 600;
        color: #069; 
    }

    #divChart p, #divMap p {
        margin: 0px 0px 0px 20px;
        font-family: sans-serif;
        line-height: 16px;
        font-size: 12px;
        font-weight: 300;
        color: #069; 
    }
    
    
	#initialMap.hidden {
		display: none;
	}

    .propertylabel {
		font-family: Helvetica, sans-serif;
		font-size: 4px;
		fill: rgb(239,101,72);
		text-anchor: middle;
        display: none;
    }

    #divStoryBox {
        width: 300px;
        height: auto;
        padding: 10px;
        background-color: #f2ecdf;
        -webkit-border-radius: 10px;
        -moz-border-radius: 10px;
        border-radius: 10px;
        -webkit-box-shadow: 4px 4px 10px rgba(0, 0, 0, 0.4);
        -moz-box-shadow: 4px 4px 10px rgba(0, 0, 0, 0.4);
        box-shadow: 4px 4px 10px rgba(0, 0, 0, 0.4);
  /*      pointer-events: none; */
    }
			
	#divStoryBox.hidden {
		display: none;
	}

    #divStoryBox p {
        margin: 0;
        font-family: sans-serif;
        font-size: 12px;
        line-height: 16px;    
    }

</style>

<div id="d3div">
    <!-- Infographic title -->
    <h4><a href="/sample_page">Rangiora township growth since 1840</a></h4>
    <!-- Step slider with play/pause automation -->
    <div id="play-controls">
        <button id="play-pause-button" class="fa fa-play" title="play"></button>
        <input id="play-range" type="range" value="2020" min="1840" max="2020" step="10" list="steplist">
        <datalist id="steplist">
            <option>1850</option>
            <option>1900</option>
            <option>1950</option>
            <option>2000</option>
        </datalist>
        <output id="play-output" for="play-range" name="year"></output>
        <div id="slider-control-values">
            <div class="align-left">1840's</div>
            <div class="align-right">2020's</div>
            <div style="clear: both;"></div>
        </div>
    </div>
    <!-- Population bar chart -->
    <div id="divChart">
        <p>Population growth</p>
    </div>
    <!-- Properties map -->
    <div id="divMap">
        <p>Property growth</p>
    </div>
    <!-- Story box for text and images -->
    <div id="divStoryBox" >
        <p><strong><span id="storyBoxTitle" style="color: #069"></span></strong></p>
        <p><span id="storyBoxText"></span></p>
        <p><span id="storyBoxCitation" style="font-size: 9px; color: #8d918d"></span></p>
        <p><span id="storyBoxDataSource" style="font-size: 9px; color: #8d918d"></span></p>
    </div>
</div>

<script src="https://d3js.org/d3.v7.min.js"></script>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
<script type="text/javascript">

    //Define Global variables
    const baseDataset = [ 
        { key: 0, decade: 1840, population: 0, color: `#004529`, title: `Waimakariri-Rakahuri Lowlands in the late 1840's`, text: `Prior to Ngati Toa's attack and destruction of Kaiapohia Pa in 1831, Ngai Tahu may have built pataka (similar to these at Te Rakawakaputa) to store weapons and food near Rangiora.  In 1848, the Crown purchased 13.5 million acres (at least two-thirds of the South Island) from Ngai Tahu for ${"\u00A3"}2000, with the land around Rangiora part of this purchase.`, href: `https://waimakaririlibraries.com/__data/assets/pdf_file/0023/13874/Chapter-1-Breaching-the-Unknown-1.pdf`, image:`src="/images/Te_Rakawakaputa_sketch.png" alt="Te Rakawakaputa on the Korotuaheka in December 1848" style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Te Rakawakaputa by William Fox in 1848, Hocken Library | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},		
        { key: 1, decade: 1850, population: 20, color: `#004529`, title: `1850's saw the first European settlers`, text: `In the late 1840's Charles Torlesse and John Boys were engaged by the Canterbury Association to survey land north of the Waimakariri.  Rangiora land was initially divided into 50-acre rural sections and sold to speculators in England and a few settlers at ${"\u00A3"}3 per acre.  Charles and Alicia Torlesse and John and Priscilla Boys were the first Europeans to build homes in Rangiora.  Alicia and Priscilla were two of six Townsend daughters who had arrived in Lyttleton with their parents in 1850 on the Cressy, one of the "first four ships" to carry emigrants from England to the new colony of Canterbury.`, href: `https://waimakaririlibraries.com/__data/assets/pdf_file/0027/13878/Chapter-6-Land-Purchase.pdf`, image:`src="/images/torlesse_home_1855.png" alt="Torlesse Home in 1855" style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: The Torlesse home by W.J.W. Hamilton in 1855, Canterbury Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},		
        { key: 2, decade: 1860, population: 1000, color: `#006837`, title: `1860's shopkeepers, tradespeople and labourers`, text: `The village of Rangiora grew along the Harewood Forest Road (now High Street) as the second wave of immigration brought shopkeepers, tradespeople and labourers into the area.  Some of the rural sections along Ashely Road were valued at ${"\u00A3"}20 per acre.`, href: `https://waimakaririlibraries.com/__data/assets/pdf_file/0021/13881/Chapter-9-The-Making-of-a-Market-Town.pdf`, image:`src="/images/thompsons_store_1860s.png" alt="John and Thomas Thompsons' store" style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},		
        { key: 3, decade: 1870, population: 1200, color: `#238443`, title: `1870s saw the railway line built through Rangiora`, text: `The coming of the railway in 1872 initiated years of growth and change; a great decade of new people, new societies, material progress and a welcome prosperity as High Street gradually matured into a market place of some importance.  1/4 acre sections near the town centre were sold at ${"\u00A3"}400 per acre in 1874.`, href: `https://waimakaririlibraries.com/__data/assets/pdf_file/0023/13883/Chapter-11-The-Eighteen-seventies.pdf`, image:`src="/images/rangiora_railway_station.png" alt="Rangiora Railway Station" style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},
        { key: 4, decade: 1880, population: 1500, color: `#41ab5d`, title: `1880's saw prosperous and stable industry`, text: `Flax and flour mills were built near Rangiora drawing their power from the streams and giving work to a large number of local men and boys.  A bacon factory was started in 1885, along with butter and cheese making and a cooperage to produce dairy equipment.`, href: `https://waimakaririlibraries.com/__data/assets/pdf_file/0027/13887/Chapter-15-The-Difficult-Years-1878-96.pdf`, image:`src="/images/rangiora _volunteer_fire_brigade.png" alt="1880 Raniora Volunteer Fire Brigade testing Rangiora water wells" style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},
        { key: 5, decade: 1890, population: 1800, color: `#78c679`, title: `1890's women petition for the right to vote`, text: `North Canterbury women joined others in New Zealand to organize a political campaign and despite the cynical attitudes of some men, petitioned the government three times before winning the right to vote in 1893.`, href: `https://waimakaririlibraries.com/__data/assets/pdf_file/0019/13861/Chapter-4-Women-win-the-vote-1880s-and-1890s.pdf`, image:`src="/images/Temperance_Hotel.png" alt="Temperance Hotel and Grand Northern Restaurant in Albert Street" style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Pauline Wood (1983) "Women of the Waimakariri"`, source:`Data source: LINZ, NZ Statistics Census Data`},
        { key: 6, decade: 1900, population: 1800, color: `#addd8e`, title: `1900's`, text: ``, href: ``, image:` style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},
        { key: 7, decade: 1910, population: 1800, color: `#d9f0a3`, title: `1910's`, text: `By the 1910's, Rangiora had still not grown much`, href: ``, image:` style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},
        { key: 8, decade: 1920, population: 2000, color: `#f7fcb9`, title: `1920's`, text: ``, href: ``, image:` style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},
        { key: 9, decade: 1930, population: 2100, color: `#ffffe5`, title: `1930's`, text: ``, href: ``, image:` style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},
        { key: 10, decade: 1940, population: 2300, color: `#fff7bc`, title: `1940's`, text: ``, href: ``, image:` style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},
        { key: 11, decade: 1950, population: 2800, color: `#fee391`, title: `1950's`, text: ``, href: ``, image:` style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},
        { key: 12, decade: 1960, population: 3500, color: `#fec44f`, title: `1960's`, text: ``, href: ``, image:` style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},
        { key: 13, decade: 1970, population: 4800, color: `#fe9929`, title: `1970's`, text: ``, href: ``, image:` style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`} ,
        { key: 14, decade: 1980, population: 6400, color: `#ec7014`, title: `1980's`, text: ``, href: ``, image:` style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},
        { key: 15, decade: 1990, population: 8800, color: `#cc4c02`, title: `1990's`, text: ``, href: ``, image:` style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},
        { key: 16, decade: 2000, population: 10800, color: `#e31a1c`, title: `2000's`, text: ``, href: ``, image:` style="height:135px; width:240px; margin-left:30px;"`, citation:`Source: Rangiora Museum | Hawkins, D N (1993) "Rangiora"`, source:`Data source: LINZ, NZ Statistics Census Data`},
        { key: 17, decade: 2010, population: 12000, color: `#bd0026`, title: `Growth after the Canterbury eqrthquakes in 2010/2011`, text: `Following the Christchurch earthquakes in 2010 and 2011, many "red zoned" residents used their Government and insurance pay-outs to build new homes in Rangiora subdivisions, recently developed on farmland beyond the original town "belts".`, href: ``, image:` style="height:135px; width:240px; margin-left:30px;"`, citation:``, source:`Data source: LINZ, NZ Statistics Census Data`},
        { key: 18, decade: 2020, population: 23000, color: `#800026`, title: `Rangiora township in 2023`, text: `Rangiora is still very much a rural township even after the significant population growth since 2010`, href: ``, image:`src="/images/rangiora_high_street_2023.png" alt="High Street in 2023 looking West from Victoria Street" style="height:135px; width:240px; margin-left:30px;"`, citation:``, source:`Data source: LINZ, NZ Statistics Census Data`}
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

    const w = 300;
    const h = 300;
    
    //HTML step slider control with play/pause button
    const playBtn = document.querySelector(`#play-pause-button`);
    let sliderTimer = undefined;

    function play(button) {         //Autoplay the slider, redrawing the Infograph at each slider step
        button.title = `pause`;
        button.className = `fa fa-pause`;
        sliderTimer = setInterval(function () {
            if (decadeValue >= decades[decades.length - 1]) {
                decadeValue = decades[0];
            } else {
                decadeValue += 10;
            }
            redraw(`autoSlide`);
        }, 1500);
    }

    function pause(button) {        //Pause the slider, either when manually moving the slider or when clicking the pause button
        button.title = `play`;
        button.className = `fa fa-play`;
        clearTimeout(sliderTimer);
        sliderTimer = undefined;
    }
    
    playBtn.addEventListener('click', function () {     //Toggle play and pause when the button has been clicked
        if (sliderTimer === undefined) {
            play(this);
        } else {
            pause(this);
        }
    });

    document.querySelector(`#play-range`).addEventListener(`input`, function (e) {      //Redraw the Infograph when the input has been manually changed
        pause(playBtn);
        decadeValue = parseInt(e.target.value);
        redraw(`manualSlide`);
    });
			
    //Create bar chart svg element, scales and axes
    const chartMargin = { top: 10, right: 10, bottom: 10, left: 10 };
    const chartWidth = 300 - chartMargin.left - chartMargin.right;
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

    const xAxis = d3.axisBottom(xScale).ticks(4, `,.3d`).tickSize(1);
    const yAxis = d3.axisLeft(yScale).tickValues([]);

    svgChart.append(`g`)
        .attr(`transform`, `translate(0,${chartHeight - 10})`)
        .call(xAxis)
        .call(g => g.select(`.domain`).remove());

    svgChart.append(`g`)
        .call(yAxis)
        .call(g => g.select(`.domain`).remove());

    //Define path generator, using the geoMercator projection
    const projection = d3
        .geoMercator()
        .scale([340000])
        .center([172.642, -43.319]);

    const path = d3.geoPath(projection);
    
    //Create map svg element and append 1840's map sketch
    const svgMap = d3
        .select(`div#divMap`)
        .append(`svg`)
        .attr(`width`, w)
        .attr(`height`, h);

    svgMap.append(`svg:image`)
        .attr(`id`, `initialMap`)
        .attr(`class`, `hidden`)
        .attr(`xlink:href`, `/images/rangiora_map_1848.png`)
        .attr(`x`, 30)
        .attr(`y`, 20)
        .attr(`width`, 250)
        .attr(`height`, 250);
             
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
                        return "transparent";
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
                
    //Function - update story box div
    function storyBox () {
        const storyBoxData = dataset[decades.indexOf(decadeValue)]
        
        d3.select(`#divStoryBox`)
            .select(`#storyBoxTitle`)
            .text(storyBoxData.title);

        d3.select(`#divStoryBox`)
            .select(`#storyBoxText`)
            .html(`<span>${storyBoxData.text}<a href="${storyBoxData.href}" target=”_blank”><img ${storyBoxData.image}></a></span>`);

        d3.select(`#divStoryBox`)
            .select(`#storyBoxCitation`)
            .text(storyBoxData.citation);

        d3.select(`#divStoryBox`)
            .select(`#storyBoxDataSource`)
            .text(storyBoxData.source);
            

        //Display the story box
        d3.select(`#divStoryBox`).classed(`hidden`, false);    
    }
    
    //Function - redraw Infograph on change of decade in slider control
    //(this happens either by manually moving the range input on the slider, or from a timer when the slider is played automatically)
    function redraw(slideMode) {

        const lastKeyValue = dataset.length - 1;
        if (decadeValue != decades[dataset.length - 1]) {
            dataset = baseDataset.slice(0,decades.indexOf(decadeValue)+1);
        }
        
        const input = document.querySelector('#play-range');
        if (slideMode = `autoSlide`) {
			input.value = decadeValue;
		}

        //Update bar chart to reflect population early in the selected decade    
        decadeValueDataObject = [baseDataset[decades.indexOf(decadeValue)]];
        yScale.domain([decadeValue])
            .padding(0.15);

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
            .attr(`width`,d => ( d.population * chartWidth / maxPopulation ))
            .attr(`fill`, d => d.color);
      
        // change map to reflect property titles issued up to and including the selected decade
        if (decadeValue != 1840) {
            d3.select(`#initialMap`).classed(`hidden`, true);
        } else {
            d3.select(`#initialMap`).classed(`hidden`, false);
        }

        svgMap.selectAll("path")
            .transition()
            .attr("fill", function(d,i) {
                //Get data value
                const value = parseInt(d.properties.decade);
                if (value && value <= decadeValue) {
                    return colors[decades.indexOf(value)];
                } else {            //If property decade does not exist or is > input decade
                    return "transparent";
            }});

        //Update the story text box with text and images relevant to the selected decade
        storyBox();				
        
   /*     d3.select(`#divStoryBox`)
            .select(`#storyBoxTitle`)
            .text(dataset[decades.indexOf(decadeValue)].title);

        d3.select(`#divStoryBox`)
            .select(`#storyBoxText`)
            .html(`<span><a href="${dataset[decades.indexOf(decadeValue)].href}" target=”_blank”><img ${dataset[decades.indexOf(decadeValue)].image}></a>${dataset[decades.indexOf(decadeValue)].text}</span>`);

        d3.select(`#divStoryBox`)
            .select(`#storyBoxCitation`)
            .text(decadeValue < 1980 ? `Source: Rangiora by D.N Hawkins, Rangiora Borough Council 1983` : ``);
        
        //Display the story text box
        d3.select(`#divStoryBox`).classed(`hidden`, false);
     */       
    }			
    
    //Function - main function that runs each of the component functions
    function runInfographic () {

        populationChart();		//Sets up initial display of the population bar chart	
        
        propertyMap();			//Sets up initial display of the properties map
        
        storyBox();				//Sets up initial display of the story box
		
    }
    
    runInfographic();

</script>

<br/>

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
