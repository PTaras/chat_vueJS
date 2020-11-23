<template>
  <div>
    <svg width="290" height="500" class="container-border"></svg>
  </div>
</template>
<script>
import * as d3 from "d3";
export default {
  data() {
    return {};
  },
  mounted() {
    let marge = { top: 5, bottom: 5, left: 5, right: 5 };
    let svg = d3.select("svg");
    let width = svg.attr("width");
    let height = svg.attr("height");
    let g = svg
      .append("g")
      .attr("transform", "translate(" + marge.top + "," + marge.left + ")");

    let nodes = [
      { name: "Taras" },
      { name: "Anton" },
      { name: "Mariya" },
      { name: "Vika" },
      { name: "Arsen" },
      { name: "Toha" },
      { name: "Misha" },
      { name: "Nika" },
      { name: "Olya" },
    ];

    let edges = [
      { source: 1, target: 4, relation: "1", value: 1.3 },
      { source: 4, target: 7, relation: "2", value: 1 },
      { source: 4, target: 6, relation: "3", value: 1 },
      { source: 4, target: 7, relation: "4", value: 1 },
      { source: 1, target: 6, relation: "5", value: 2 },
      { source: 2, target: 5, relation: "6", value: 0.9 },
      { source: 3, target: 7, relation: "7", value: 1 },
      { source: 5, target: 6, relation: "8", value: 1.6 },
      { source: 6, target: 7, relation: "9", value: 0.7 },
      { source: 6, target: 8, relation: "0", value: 2 },
    ];

    let colorScale = d3
      .scaleOrdinal()
      .domain(d3.range(nodes.length))
      .range(d3.schemeCategory10);

    let forceSimulation = d3
      .forceSimulation()
      .force("link", d3.forceLink())
      .force("charge", d3.forceManyBody())
      .force("center", d3.forceCenter());

    forceSimulation.nodes(nodes).on("tick", ticked);

    forceSimulation
      .force("link")
      .links(edges)
      .distance(function (d) {
        return d.value * 100;
      });

    forceSimulation
      .force("center")
      .x(width / 2)
      .y(height / 2);

    let links = g
      .append("g")
      .selectAll("line")
      .data(edges)
      .enter()
      .append("line")
      .attr("stroke", function (d, i) {
        return colorScale(i);
      })
      .attr("stroke-width", 1);

    let linksText = g
      .append("g")
      .selectAll("text")
      .data(edges)
      .enter()
      .append("text")
      .text(function (d) {
        return d.relation;
      });

    let gs = g
      .selectAll(".circleText")
      .data(nodes)
      .enter()
      .append("g")
      .attr("transform", function (d) {
        let cirX = d.x;
        let cirY = d.y;
        return "translate(" + cirX + "," + cirY + ")";
      })
      .call(
        d3.drag().on("start", started).on("drag", dragged).on("end", ended)
      );

    gs.append("circle")
      .attr("r", 10)
      .attr("fill", function (d, i) {
        return colorScale(i);
      });

    gs.append("text")
      .attr("x", -10)
      .attr("y", -20)
      .attr("dy", 10)
      .text(function (d) {
        return d.name;
      });
    // ticked
    function ticked() {
      links
        .attr("x1", function (d) {
          return d.source.x;
        })
        .attr("y1", function (d) {
          return d.source.y;
        })
        .attr("x2", function (d) {
          return d.target.x;
        })
        .attr("y2", function (d) {
          return d.target.y;
        });
      linksText
        .attr("x", function (d) {
          return (d.source.x + d.target.x) / 2;
        })
        .attr("y", function (d) {
          return (d.source.y + d.target.y) / 2;
        });
      gs.attr("transform", function (d) {
        return "translate(" + d.x + "," + d.y + ")";
      });
    }
    // drag
    function started(d) {
      if (!d3.event.active) {
        forceSimulation.alphaTarget(0.8).restart();
      }
      d.fx = d.x;
      d.fy = d.y;
    }
    function dragged(d) {
      d.fx = d3.event.x;
      d.fy = d3.event.y;
    }
    function ended(d) {
      if (!d3.event.active) {
        forceSimulation.alphaTarget(0);
      }
      d.fx = null;
      d.fy = null;
    }
  },
};
</script>
<style scoped>
</style>