<script>
  import * as _ from "lodash-es";

  export let data;

  export let getX; // function to get X data
  export let getY; // function to get Y data

  export let graphName;

  let dataPoints, dataPointsAsString, graph, countAtX, dateAtX, minY, maxY;

  let xPos = 0;
  let showDetail = false;

  let width = window.innerWidth - 200;

  $: height = width * 0.33;
  $: numberOfPoints = Math.floor(_.size(data) - 1);

  $: gapAsIndex = _.size(data) / (numberOfPoints + 1);
  $: gapAsXDistance = width / (numberOfPoints + 1);

  $: minX = getX(data[0]);
  $: maxX = getX(_.last(data));

  $: if (data || width) {
    let counts = _.values(data).map((r) => getY(r));
    minY = _.min(counts);
    maxY = _.max(counts);

    dataPoints = getDataPoints();
    dataPointsAsString = getPointsAsString();
  }

  function translateYAxis(value) {
    if (maxY == minY) {
      return height;
    }
    return height - Math.round(((value - minY) / (maxY - minY)) * height);
  }

  function getDataPoints() {
    let points = []; // xAxis, yAxis, date, count

    let firstCount = getY(data[0]);
    points.push([0, translateYAxis(firstCount), minX, firstCount]);

    // points in between
    for (var i = 1; i <= numberOfPoints; i++) {
      var index = Math.floor(i * gapAsIndex);
      var count = getY(data[index]);
      var date = getX(data[index]);
      points.push([
        Math.round(i * gapAsXDistance),
        translateYAxis(count),
        date,
        count,
      ]);
    }
    let lastCount = getY(_.last(data));
    points.push([width, translateYAxis(lastCount), maxX, lastCount]);
    return points;
  }

  function getPointsAsString() {
    return dataPoints.map((p) => p.slice(0, 2).join(",")).join(" ");
  }

  function handleResize() {
    width = window.innerWidth - 200;
  }

  function handleMouseMove(e) {
    var dim = graph.getBoundingClientRect();
    xPos = e.x - dim.x;
    let index = getIndexAtX();
    countAtX = getY(data[index]);
    dateAtX = getX(data[index]);
    showDetail = true;
  }

  function handleMouseLeave(e) {
    xPos = 0;
    showDetail = false;
  }

  function getIndexAtX() {
    let index = Math.floor((xPos / width) * _.size(data));
    if (index < 0) {
      index = 0;
    }
    if (index > _.size(data) - 1) {
      index = _.size(data) - 1;
    }

    return index;
  }
</script>

<svelte:window on:resize={handleResize} />

<main>
  <svg
    bind:this={graph}
    width={width + 5}
    {height}
    on:mousemove={handleMouseMove}
    on:mouseleave={handleMouseLeave}
  >
    <g class="x" transform="translate(0,-30)">
      <text x={width / 2}>{graphName}</text>
    </g>

    <!-- x axis -->
    <!-- svelte-ignore component-name-lowercase -->
    <line x1="0" x2={width + 5} y1={height} y2={height} />
    {#if showDetail}
      <line x1={xPos} x2={xPos} y1="0" y2={height} />
      <text y="0" x={xPos}>{countAtX}</text>
      <text y={height + 20} x={xPos}>{dateAtX}</text>
    {:else}
      <g class="x" transform="translate(0,{height + 20})">
        <text x="0">{minX}</text>
        <text x={width}>{maxX}</text>
      </g>
    {/if}

    <!-- y axis -->
    <!-- svelte-ignore component-name-lowercase -->
    <line x1="0" x2="0" y1="0" y2={height} />
    <g class="y" transform="translate(-10,0)">
      <text y={height}>{minY}</text>
      <text y="0">{maxY}</text>
    </g>

    <!-- line for the graph -->
    <polyline style="stroke: red; stroke-width: 2" points={dataPointsAsString} />

    <!-- data points -->
    {#each dataPoints as point}
      <circle
        on:click={console.log(point)}
        cx={point[0]}
        cy={point[1]}
        r={width / 500}
      />
    {/each}
  </svg>
</main>

<style>
  svg {
    overflow: visible;
    margin: 100px;
  }

  line,
  polyline {
    fill: none;
    stroke: black;
  }

  .x text {
    text-anchor: middle;
  }

  .y text {
    text-anchor: end;
  }

  text {
    font-size: 10px;
  }
</style>
