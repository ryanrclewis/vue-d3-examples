<template>
  <div>
    <h2>Force-Direct Graph</h2>
    <svg width="960" height="600"></svg>
  </div>
</template>
<script>
import * as d3 from 'd3'
export default {
  data () {
    return {}
  },
  mounted () {
    let marge = { top: 60, bottom: 60, left: 60, right: 60 }
    let svg = d3.select('svg')
    let width = svg.attr('width')
    let height = svg.attr('height')
    let g = svg.append('g')
      .attr('transform', 'translate(' + marge.top + ',' + marge.left + ')')

    // 准备数据
    // 节点集
    let baseNodes = [
    //coetic org
    { id: "coetic", group_id: 0, label: "Coetic", level: 1 },
    //groups
    { id: "engineer", group_id: 0, label: "Engineers", level: 2 },
    { id: "customer", group_id: 3, label: "Customer Experience", level: 2 },
    { id: "consultant", group_id: 1, label: "Consulting & Content", level: 2 },
    //groups
    { id: "woether", group_id: 0, label: "Woether", level: 3 },
    { id: "em360", group_id: 0, label: "EM360", level: 3 },
    //members
    { id: "rick", group_id: 2, label: "Rick DeShon", role: "", level: 4 },
    { id: "karen", label: "Karen DeShon", level: 4 },
    { id: "sam", label: "Sam Coffey", level: 4 },
    { id: "rebecca", label: "Rebecca Sinkoff", level: 4 },
    { id: "scott", label: "Scott James", level: 4 },
    { id: "konglin", label: "Konglin Zhu", level: 4 },
    { id: "austin", label: "Austin Pfeil", level: 4 },
    { id: "noah", label: "Noah Stewart", level: 4 },
    { id: "hadley", label: "Hadley Gaines", level: 4 },
    { id: "jake", label: "Jake Bosio", level: 4 },
    { id: "owen", label: "Owen O'Brien", level: 4 },
    { id: "megan", label: "Megan Jursch", level: 4 },
    { id: "grace", label: "Grace Martin", level: 4 },
    { id: "ryan", label: "Ryan R. C. Lewis", level: 4 },
    { id: "stu", label: "Stuart Clapp", level: 4 },
    { id: "zach", label: "Zach Martis", level: 4 },
  ];
    // 边集
    let baseLinks = [
    //coetic org links
    { target: "engineer", source: "coetic", strength: 0.1 },
    { target: "consultant", source: "coetic", strength: 0.1 },
    { target: "customer", source: "coetic", strength: 0.1 },
    //coetic org links
    { target: "woether", source: "coetic", strength: 0.1 },
    { target: "em360", source: "coetic", strength: 0.1 },
    //sam links
    { target: "em360", source: "sam", strength: 0.1 },
    { target: "engineer", source: "sam", strength: 0.1 },
    //rebecca links
    { target: "woether", source: "rebecca", strength: 0.1 },
    { target: "engineer", source: "rebecca", strength: 0.1 },

    //scott links
    { target: "woether", source: "scott", strength: 0.1 },
    { target: "engineer", source: "scott", strength: 0.1 },
    //konglin links
    { target: "engineer", source: "konglin", strength: 0.1 },
    //austin links
    { target: "em360", source: "austin", strength: 0.1 },
    { target: "engineer", source: "austin", strength: 0.1 },
    //noah links
    { target: "em360", source: "noah", strength: 0.1 },
    { target: "engineer", source: "noah", strength: 0.1 },
    //hadley links
    { target: "woether", source: "hadley", strength: 0.1 },
    { target: "engineer", source: "hadley", strength: 0.1 },
    //jake links
    { target: "em360", source: "jake", strength: 0.1 },
    { target: "engineer", source: "jake", strength: 0.1 },
    //owen links
    { target: "woether", source: "owen", strength: 0.1 },
    { target: "engineer", source: "owen", strength: 0.1 },
    //megan links
    { target: "consultant", source: "megan", strength: 0.1 },
    //grace links
    { target: "consultant", source: "grace", strength: 0.1 },
    //rick links
    { target: "coetic", source: "rick", strength: 0.1 },
    { target: "engineer", source: "rick", strength: 0.1 },
    { target: "customer", source: "rick", strength: 0.1 },
    { target: "em360", source: "rick", strength: 0.1 },
    { target: "woether", source: "rick", strength: 0.1 },
    //karen links
    { target: "coetic", source: "karen", strength: 0.1 },
    { target: "consultant", source: "karen", strength: 0.1 },
    { target: "customer", source: "karen", strength: 0.1 },
    //ryan links
    { target: "customer", source: "ryan", strength: 0.1 },
    { target: "woether", source: "ryan", strength: 0.1 },
    { target: "em360", source: "ryan", strength: 0.1 },
    //stu links
    { target: "customer", source: "stu", strength: 0.1 },
    { target: "em360", source: "stu", strength: 0.1 },
    { target: "woether", source: "stu", strength: 0.1 },
    //zach links
    { target: "customer", source: "zach", strength: 0.1 },
    { target: "woether", source: "zach", strength: 0.1 },
  ]
    // 设置一个颜色比例尺
    let colorScale = d3.scaleOrdinal()
      .domain(d3.range(nodes.length))
      .range(d3.schemeCategory10)
    // 新建一个力导向图
    let forceSimulation = d3.forceSimulation()
      .force('link', d3.forceLink())
      .force('charge', d3.forceManyBody())
      .force('center', d3.forceCenter())
    // 生成节点数据
    forceSimulation.nodes(nodes)
      .on('tick', ticked)
    // 生成边数据
    forceSimulation.force('link')
      .links(edges)
      .distance(function (d) { // 每一边的长度
        return d.value * 100
      })
    // 设置图形中心位置
    forceSimulation.force('center')
      .x(width / 2)
      .y(height / 2)
    // // 顶点集，边集
    // console.log(nodes)
    // console.log(edges)
    // 绘制边
    let links = g.append('g')
      .selectAll('line')
      .data(edges)
      .enter()
      .append('line')
      .attr('stroke', function (d, i) {
        return colorScale(i)
      })
      .attr('stroke-width', 1)
    // 边上的文字
    let linksText = g.append('g')
      .selectAll('text')
      .data(edges)
      .enter()
      .append('text')
      .text(function (d) {
        return d.relation
      })
    // 创建分组
    let gs = g.selectAll('.circleText')
      .data(nodes)
      .enter()
      .append('g')
      .attr('transform', function (d) {
        let cirX = d.x
        let cirY = d.y
        return 'translate(' + cirX + ',' + cirY + ')'
      })
      .call(d3.drag()
        .on('start', started)
        .on('drag', dragged)
        .on('end', ended)
      )
    // 绘制节点
    gs.append('circle')
      .attr('r', 10)
      .attr('fill', function (d, i) {
        return colorScale(i)
      })
    // 文字
    gs.append('text')
      .attr('x', -10)
      .attr('y', -20)
      .attr('dy', 10)
      .text(function (d) {
        return d.name
      })
    // ticked
    function ticked () {
      links
        .attr('x1', function (d) { return d.source.x })
        .attr('y1', function (d) { return d.source.y })
        .attr('x2', function (d) { return d.target.x })
        .attr('y2', function (d) { return d.target.y })
      linksText
        .attr('x', function (d) { return (d.source.x + d.target.x) / 2 })
        .attr('y', function (d) { return (d.source.y + d.target.y) / 2 })
      gs
        .attr('transform', function (d) { return 'translate(' + d.x + ',' + d.y + ')' })
    }
    // drag
    function started (d) {
      if (!d3.event.active) {
        forceSimulation.alphaTarget(0.8).restart() // 设置衰减系数，对节点位置移动过程的模拟，数值越高移动越快，数值范围[0, 1]
      }
      d.fx = d.x
      d.fy = d.y
    }
    function dragged (d) {
      d.fx = d3.event.x
      d.fy = d3.event.y
    }
    function ended (d) {
      if (!d3.event.active) {
        forceSimulation.alphaTarget(0)
      }
      d.fx = null
      d.fy = null
    }
  }
}
</script>
<style scoped>
</style>
