---
layout: page
permalink: /research/
title: research
description:
nav: true
nav_order: 2
---

I focus on integrating `machine learning techniques within process systems engineering (PSE)`. PSE encompasses a range of methods, including (i) Modeling/Simulation, (ii) Data Analytics for process monitoring, (iii) Process Design, (iv) Process Synthesis, (v) Optimization, and (vi) Control. These approaches address challenges across various engineering domains: (i) Energy Systems (Sustainability), (ii) Supply Chain Systems (Smart Manufacturing), (iii) Molecular Systems (Novel Materials), and (iv) Biosystems (Effective Healthcare).

The following visualization illustrates the role of PSE:
<!-- <!DOCTYPE html> -->
<meta charset="utf-8">
<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/3.5.17/d3.min.js"></script>
<script src="https://d3js.org/d3.v3.min.js"></script>
<style>

.svg-container {
  display: block;
  border: none; /* Adjusted border for visibility */
  position: relative;
  width: 80%; /* Custom width */
  padding-bottom: 60%; /* Aspect ratio adjustment */
  vertical-align: middle;
  overflow: auto; /* Enable scroll if content overflows */
  background-color: #f9f9f9; /* Light background color */
  box-shadow: 3px 3px 8px rgba(0, 0, 0, 0.2); /* Subtle shadow */
  margin: 20px auto; /* Center align with margin */
  cursor: pointer; /* Change cursor to pointer */
  opacity: 0.8; /* 80% transparency */
}

.svg-content {
  display: inline-block;
  position: absolute;
  top: 0;
  left: 0;
}

path.link {
  fill: none;
  stroke: #31352e;
  stroke-width: 1.5px;
}
 
path.link.phdCurrent {
  stroke: #FF0000;
  stroke-width: 2px;
}
 
path.link.phdGraduate {
  stroke: #FF7F50;
  stroke-width: 2px;
}

path.link.mtechCurrent {
  stroke: #32CD32;
  stroke-width: 2px;
}

path.link.mtechGraduate {
  stroke: #00BFFF;
  stroke-width: 2px;
}

path.link.ddCurrent {
  stroke: #7B68EE;
  stroke-width: 2px;
}
 
path.link.ddGraduate {
  stroke: #FF4500;
  sstroke-width: 2px;
}

path.link.unknown {
  stroke: #b3b3b3;
}
 
circle {
  fill: #ccc;
  stroke: #292929;
  stroke-width: 1px;
}
 
text {
  fill: #000;
  font: 25px sans-serif;
  pointer-events: none;
}
 
</style>
<body>
  <header>
  </header>
  <!-- container for force layout visualisation  -->
  <div id="container" class="svg-container">
  </div>  
<script>
 
// fetch the data
d3.csv("/sa-prana/assets/data/automation_net.csv", function(error, links) {

var nodes = {},
  nodeGroup = {};

// Compute the distinct nodes from the links.
links.forEach(function(link) {
    link.source = nodes[link.source] || 
        (nodes[link.source] = {nameS: link.source});
    link.target = nodes[link.target] || 
        (nodes[link.target] = {nameT: link.target});
    link.value = +link.value;
    nodeGroup[link.target.nameT] = link.group;});
 
var width = 960,
    height = 800,
    color = d3.scale.category10();
 
var force = d3.layout.force()
    .nodes(d3.values(nodes))
    .links(links)
    .size([width, height])
    .linkStrength(0.5)//** by default 1
    .friction(0.9)
    .linkDistance(linkLength)
    .charge(-10000) //** -ve value: repulsion
    .gravity(0.05) //** spring attraction btw nodes.
    .theta(0.8)
    .alpha(0.1)
    .on("tick", tick)
    .start();

// Initial positions for PSE, technique nodes, and application nodes
nodes["PSE"].fx = width * 0.15; // Fix PSE on the left
nodes["PSE"].fy = height * 0.5;

const techniqueNodes = ["Modeling/Simulation", "Optimization", "Process Synthesis", "Control", "Process Design", "Data Analytics"];
const applicationNodes = ["Energy Systems", "Molecular Systems", "Supply Chain Systems", "Biosystems"];

// Set initial positions for technique nodes (clustered towards the center)
techniqueNodes.forEach(function (node, i) {
    nodes[node].x = width * 0.35 + Math.random() * 100; // Randomize a bit
    nodes[node].y = height * 0.5 + i * 60;
});

// Set initial positions for application nodes (on the right)
applicationNodes.forEach(function (node, i) {
    nodes[node].x = width * 0.65 + Math.random() * 100;
    nodes[node].y = height * 0.3 + i * 100;
});


links.forEach(function(link) {
	if (link.value == 1) {
		link.type = "phdCurrent";
	} else if (link.value == 2) {
		link.type = "phdGraduate";
  } else if (link.value == 3) {
    link.type = "mtechCurrent";
  } else if (link.value == 4) {
    link.type = "mtechGraduate";
  } else if (link.value == 5) {
    link.type = "ddCurrent";
  } else if (link.value == 6) {
    link.type = "ddGraduate";
  } else {
    link.type = "unknown";
  }
});
 
var svg = d3.select("div#container").append("svg")
    .attr("preserveAspectRatio", "xMinYMin meet")
    .attr("viewBox", "10 -20 960 800") //** Manually enter width and height. should be same as earlier defined
    .classed("svg-content", true);
    //.attr("width", width)
    //.attr("height", height);
 
// build the arrow.
svg.append("svg:defs").selectAll("marker")
    .data(["end"])      // Different link/path types can be defined here
  .enter().append("svg:marker")    // This section adds in the arrows
    .attr("id", String)
    .attr("viewBox", "0 -5 10 10")
    .attr("refX", 15)
    .attr("refY", -1.5)
    .attr("markerWidth", 6)
    .attr("markerHeight", 6)
    .attr("orient", "auto")
  .append("svg:path")
    .attr("d", "M0,-5L10,0L0,5");
 
// define the nodes
var node = svg.selectAll(".node")
    .data(force.nodes())
  .enter().append("g")
    .attr("class", "node")
    .attr("cursor", "pointer")
    .on("mouseover", mouseover)
    .on("mouseout", mouseout)
    .call(force.drag);
    
// add the links and the arrows
var path = svg.append("svg:g").selectAll("path")
    .data(force.links())
  .enter().append("svg:path")
    .attr("class", function(d) { return "link " + d.type; })
    .attr("marker-end", "url(#end)");
 

     
// add the nodes
node.append("circle")
    .attr("r", function(d)
      {if (d.nameS) {
        return 20;} /* radius for source node */
      if (d.nameT) {
        return 20;}}) /* radius for target node */
    .style("fill", function(d)
      {if (d.nameS) {
        return "red";} /* color for source node */
      if (d.nameT) {
        return color(nodeGroup[d.nameT]);} /* color for target node */
      return "#7a7a7a"; }); 

// add the text 
node.append("text")
    .attr("x", 12)
    .attr("dy", ".35em")
    .text(function(d) 
    {if (d.nameS) {
        return d.nameS;} 
      if (d.nameT) {
        return d.nameT;}});
 
// add the curvy lines
function tick() {
    path.attr("d", function(d) {
        var dx = d.target.x - d.source.x,
            dy = d.target.y - d.source.y,
            dr = Math.sqrt(dx * dx + dy * dy);
        return "M" + 
            d.source.x + "," + 
            d.source.y + "A" + 
            dr + "," + dr + " 0 0,1 " + 
            d.target.x + "," + 
            d.target.y;
    });
 
    node
        .attr("transform", function(d) { 
		    return "translate(" + d.x + "," + d.y + ")"; });
}
 
// action to take on mouse over
function mouseover() {
    d3.select(this).select("text").transition()
        .duration(750)
        .ease("elastic")
        .attr("x", 22)
        .style("fill", "#39ff14")        // Bright green text color
        .style("stroke", "black")        // Black outline
        .style("stroke-width", "1px")    // Thicker outline
        .style("font", "35px times");
    d3.select(this).select("circle").transition()
        .duration(750)
        .ease("elastic")
        .attr("r", 30);
}
 
// action to take on mouse double out
function mouseout() {
    d3.select(this).select("circle").transition()
        .duration(200)
        .ease("quad")
        .attr("r", function(d)
          {if (d.nameS) {
            return 20;} 
          if (d.nameT) {
            return 20;}});
    d3.select(this).select("text").transition()
        .duration(200)
        .ease("quad")
        .attr("x", 12)
        .style("stroke", "none")
        .style("fill", "black")
        .style("stroke", "none")
        .style("font", "25px sans-serif");
}

function linkLength(link){
	if (link.value == 1 || link.value == 1 || link.value == 1) {
		return 60;
	} else if (link.value == 1 || link.value == 1 || link.value == 1) {
		return 50;
  } else {
    return 50;
  }
}

});
 
</script>
</body>
<!-- </html> -->

A detailed research statement will be populated soon  <i class="fa-solid fa-cloud-arrow-up"></i><i class="fa-solid fa-spinner"></i>
