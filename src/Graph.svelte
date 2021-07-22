<script>
  import * as _ from "lodash-es";

  export let data;
  export let dose;
  export let stat;

  let graphName,
    points,
    pointsAsString,
    minCount,
    maxCount,
    graph,
    countAtX,
    dateAtX;

  let xPos = 0;
  let showDetail = false;

  let width = window.innerWidth - 200;

  $: height = width * 0.33;
  $: numberOfXMarkers = Math.floor(_.size(data) - 1);

  $: gapAsIndex = _.size(data) / (numberOfXMarkers + 1);
  $: gapAsXDistance = width / (numberOfXMarkers + 1);

  $: minDate = data[0].date;
  $: maxDate = _.last(data).date;

  $: if (data || width || stat || dose) {
    if (dose == 2) {
      graphName = `${stat} (2 Doses)`;
    } else if (dose == 1) {
      graphName = `${stat} (1 Dose)`;
    }

    if (stat === "Total") {
      minCount = getCount(data[0]);
      maxCount = getCount(_.last(data));
    } else {
      let counts = _.values(data).map((r) => getCount(r));
      minCount = _.min(counts);
      maxCount = _.max(counts);
    }

    points = getPoints();
    pointsAsString = getPointsAsString();
  }

  function getCount(d) {
    if (stat === "Total") {
      if (dose == 1) {
        return parseInt(d.dose1_cumul);
      } else if (dose == 2) {
        return parseInt(d.dose2_cumul);
      }
    } else if (stat === "Daily") {
      if (dose == 1) {
        return parseInt(d.dose1_daily);
      } else if (dose == 2) {
        return parseInt(d.dose2_daily);
      }
    }
  }

  function translateYAxis(value) {
    if (maxCount == minCount) {
      return height;
    }
    return (
      height - Math.round(((value - minCount) / (maxCount - minCount)) * height)
    );
  }

  function getPoints() {
    let points = []; // xAxis, yAxis, date, count
    let firstCount = getCount(data[0]);
    points.push([0, translateYAxis(firstCount), minDate, firstCount]);

    // points in between
    for (var i = 1; i <= numberOfXMarkers; i++) {
      var index = Math.floor(i * gapAsIndex);
      var count = getCount(data[index]);
      var date = data[index].date;
      points.push([
        Math.round(i * gapAsXDistance),
        translateYAxis(count),
        date,
        count,
      ]);
    }
    let lastCount = getCount(_.last(data));
    points.push([width, translateYAxis(lastCount), maxDate, lastCount]);
    return points;
  }

  function getPointsAsString() {
    let points = getPoints();
    let pointsAsString = points.map((p) => p.slice(0, 2).join(",")).join(" ");
    return pointsAsString;
  }

  function handleResize() {
    width = window.innerWidth - 200;
  }

  function handleMouseMove(event) {
    let e = graph;
    var dim = e.getBoundingClientRect();
    xPos = event.x - dim.x;
    let index = getIndexAtX();
    countAtX = getCount(data[index]);
    dateAtX = data[index].date;
    showDetail = true;
  }

  function handleMouseLeave(event) {
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
        <text x="0">{minDate}</text>
        <text x={width}>{maxDate}</text>
      </g>
    {/if}

    <!-- y axis -->
    <!-- svelte-ignore component-name-lowercase -->
    <line x1="0" x2="0" y1="0" y2={height} />
    <g class="y" transform="translate(-10,0)">
      <text y={height}>{minCount}</text>
      <text y="0">{maxCount}</text>
    </g>

    <!-- line for the graph -->
    <polyline style="stroke: red; stroke-width: 2" points={pointsAsString} />

    <!-- data points -->
    {#each points as point}
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
