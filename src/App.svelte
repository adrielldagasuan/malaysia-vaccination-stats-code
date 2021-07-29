<script>
  import { onMount } from "svelte";
  import * as _ from "lodash-es";
  import * as Papa from "papaparse";
  import State from "./State.svelte";

  let data, population;
  let uniqueDates = [];
  let uniqueStates = [];
  let recordsByState;
  let recordsByDate;
  let horizontalLayout = true;
  let stateShown = "";

  onMount(async () => {
    handleWindowResize();
    const vax = await fetch("./static/vax_state.csv");
    const pop = await fetch("./static/population.csv");
    const vaxText = await vax.text();
    const popText = await pop.text();

    data = Papa.parse(vaxText, { header: true }).data;
    population = Papa.parse(popText, { header: true }).data;

    var objects = _.entries(data)
      .map((r) => {
        return r[1];
      })
      .filter((r) => r.date !== "");

    recordsByDate = _.groupBy(objects, (r) => {
      if (r.date !== "") {
        return r.date;
      }
    });
    uniqueDates = _.keys(recordsByDate);

    recordsByState = _.groupBy(objects, (r) => {
      return r.state;
    });
    uniqueStates = _.keys(recordsByState);
  });

  function getDataForState(state) {
    return _.values(recordsByState[state]);
  }

  function handleWindowResize() {
    if (window.innerWidth < 500) {
      horizontalLayout = false;
    } else {
      horizontalLayout = true;
    }
  }

  function selectState(state) {
    stateShown = stateShown === state ? "" : state;
  }
</script>

<svelte:window on:resize={handleWindowResize} />

<main>
  <div class="container">
    <div class="center">Malaysia Covid-19 Vaccination Stats</div>
    {#if horizontalLayout}
      <ul class="center menu">
        {#each uniqueStates as state}
          <li 
            class="clickable {stateShown === state ? "selected" : ""}"
            on:click={() => selectState(state)}
          >
            {state}
          </li>
        {/each}
      </ul>
    {/if}

    {#if (stateShown === "" && horizontalLayout) || !horizontalLayout}
      <div class="center">Select a state to view the statistics</div>
    {/if}

    {#each uniqueStates as state}
      <!-- Show selection above expected graph if layout is vertical -->
      {#if !horizontalLayout}
        <div
          class="wrapper clickable {stateShown === state ? 'selected' : ''}"
          on:click={() => selectState(state)}
        >
          <div class="grid">{state}</div>
        </div>
      {/if}
      {#if stateShown === state}
        <State allData={getDataForState(state)} />
      {/if}
    {/each}
  </div>
</main>

<style>
  .container {
    display: grid;
    gap: 10px;
    grid-auto-rows: min-content(30px);
  }
  .center {
    display: grid;
    justify-content: center;
  }

  .grid {
    padding: 0.5em;
    display: grid;
    justify-content: space-around;
  }

  .wrapper {
    display: grid;
    gap: 10px;
  }
  .wrapper:hover {
    background-color: rgb(255, 123, 75);
  }

  .menu {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
    max-width: 720px;
    margin: 0 auto;
    list-style: none;
  }

  .menu li {
    padding: 1%;
  }

  .menu li:hover {
    background-color: rgb(255, 123, 75);
  }

  .selected {
    background-color: rgb(255, 123, 75);
  }

  .clickable {
    cursor: pointer;
  }
</style>
