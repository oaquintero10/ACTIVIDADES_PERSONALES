# MEDIDAS SENSORES DE UNA BANDA
Esto es un ejemplo que hay que modificar para adecuarlo al anáisis

<style>
body2 {
  font: 10px sans-serif;
}
svg2 {
  font: 10px sans-serif;
}
.axis path,
.axis line {
  fill: none;
  stroke: #000;
  shape-rendering: crispEdges;
}
.x.axis path {
  fill:none;
  stroke:#001;
  shape-rendering: crispEdges;
}
.line {
  fill: none;
  stroke-width: 3px;
}
</style>
<body>
<h1>PULSO DURANTE TODO EL DIA!!!</h1  >
<script src="https://d3js.org/d3.v3.js"></script>
<script>

var parseDate = d3.time.format("%Y-%m-%d").parse;

var margin = {top: 20, right: 80, bottom: 40, left: 50},
    width = 900 - margin.left - margin.right,
    height = 500 - margin.top - margin.bottom;

var x = d3.scale.linear()
    .range([0, width])
    .domain([0, 24]);

var y = d3.scale.linear()
    .range([height,0])
    .domain([40,170]);

var color = d3.scale.category10();

var xAxis = d3.svg.axis()
    .scale(x)
    .orient("bottom");

var yAxis = d3.svg.axis()
    .scale(y)
    .orient("left");

var line = d3.svg.line()
    .interpolate("basis")
    .x(function(d) { return x(d['percentRemaining']); })
    .y(function(d) { return y(d['heartRate']); });

var svg2 = d3.select("body").append("svg")
    .attr("width", width + margin.left + margin.right)
    .attr("height", height + margin.top + margin.bottom)
  .append("g")
    .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

d3.csv("merged.csv", function(error, data) {

     color.domain([0,1,2,3,4,5,6,7,8,9,10,11,12]);

  data = data.map( function (d) {
    return {
      lookup: d['lookup'],
      date: parseDate(d['lookup']),
      percentRemaining: +d['percentRemaining']*100,
      heartRate: Math.round(d['heart_rate(bpm)']) };
});

  data = d3.nest()
  .key(function(d) { return d.lookup; })
  .sortKeys(d3.ascending)
  .sortValues(function(a,b) { return b.percentRemaining - a.percentRemaining; } )
  .entries(data);

console.log(data)

  svg2.append("g")
      .attr("class", "x axis")
      .attr("transform", "translate(0," + height + ")")
      .call(xAxis);

  svg2.append("g")
      .attr("class", "y axis")
      .call(yAxis);

  svg2.append("text")
    .attr("x",0)
    .attr("y",-5)
    .attr("transform","rotate(90)")
    .text("Pulso (BPM)")

 svg2.append("path")
     .attr("d", function(d) { return "M 0," + y(100) + ", L " + width + "," + y(100); })
     .attr("clip-path", "url(#rect-clip)")
     .style("stroke", "#000")
     .style("fill","none")
     .style("stroke-width", 2);

 svg2.append("path")
     .attr("d", function(d) { return "M 0," + y(50) + ", L " + width + "," + y(50); })
     .attr("clip-path", "url(#rect-clip)")
     .style("stroke", "#000")
     .style("fill","none")
     .style("stroke-width", 2);

  // define the clipPath
  svg2.append("clipPath")
      .attr("id", "rect-clip")
    .append("rect")
      .attr("width", width)
      .attr("height", height) ;

  var runs = svg2.selectAll(".runs")
      .data(data, function(d) { return d.key; })
    .enter().append("g")
      .attr("id", function(d) { return d.key; })
      .attr("class", "city");

  runs.append("text")
    .attr("class",function(d) { return "linetext" + d.key })
    .text(function(d) { return d.key })
    .attr("x", width - 130)
    .attr("y", 20)
    .style("fill", "none")
    .style("font", "25px sans-serif");

  runs.append("path")
      .attr("class","line")
      .attr("d", function(d) { return line(d.values); })
      .attr("clip-path", "url(#rect-clip)")
      .style("stroke", "#000")
      .style("stroke-opacity", .2)
      .on("mouseover", function() {

        d3.select(this)
        .transition()
        .duration(1)
        .style("stroke", "red")
        .style("stroke-width", 5)
        .style("stroke-opacity", 1);

        d3.selectAll(".linetext" + this.parentNode.id)
        .style("fill","#000");

      })
      .on("mouseout", function() {

        d3.select(this)
        .transition()
        .duration(10)
        .style("stroke", "#000")
        .style("stroke-width", 0.5);

        d3.selectAll(".linetext" + this.parentNode.id)
        .style("fill","none");

      });

});

</script>


- Pulso 1
- Pulso 2


### Autor

Omar Alfonso Quintero Contreras
Código: 200210675
Universidad de los Andes
Maestría en Ingeniería de la Información<br> Visual Analytics
