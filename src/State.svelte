<script>
  import Graph from "./Graph.svelte";
  import DateSelect from "./DateSelect.svelte";
  import _ from "lodash-es";
  export let allData;
  export let state;
  let data = allData;
  let graphEnabled = false;

  let minDate = allData[0].date;
  let maxDate = _.last(allData).date;

  let startDate = minDate;
  let endDate = maxDate;

  let selectedDose = 2;
  let selectedStat = "Total";

  $: if (startDate || endDate) {
    let startIndex = _.findIndex(allData, (r) => {
      return r.date === startDate;
    });

    let endIndex = _.findIndex(allData, (r) => {
      return r.date === endDate;
    });

    data = _.slice(allData, startIndex, endIndex + 1);
  }

  function toggleGraph() {
    graphEnabled = !graphEnabled;
  }

  function handleDose(dose) {
    selectedDose = dose;
  }

  function handleStat(stat) {
    selectedStat = stat;
  }
</script>

<main>
  <div class="center">------------------------------------</div>
  <div class="wrapper" on:click={toggleGraph}>
    <div class="grid">{state}</div>
  </div>
  {#if graphEnabled}
    <div class="options">
      <div class="left">
        <button
          on:click={() => {
            handleStat("Total");
          }}
          class={selectedStat === "Total" ? "selected" : ""}>Total</button
        >
      </div>
      <div class="right">
        <button
          on:click={() => {
            handleStat("Daily");
          }}
          class={selectedStat === "Daily" ? "selected" : ""}>Daily</button
        >
      </div>
    </div>
    <div class="options">
      <div class="left">
        <button
          on:click={() => {
            handleDose(2);
          }}
          class={selectedDose == 2 ? "selected" : ""}>Full</button
        >
      </div>
      <div class="right">
        <button
          on:click={() => {
            handleDose(1);
          }}
          class={selectedDose == 1 ? "selected" : ""}>Single</button
        >
      </div>
    </div>
    <DateSelect bind:startDate bind:endDate {minDate} {maxDate} />
    <Graph {data} dose={selectedDose} stat={selectedStat} />
  {/if}
</main>

<style>
  .center {
    display: grid;
    justify-content: center;
  }
  .wrapper {
    display: grid;
    gap: 10px;
  }
  .wrapper:hover {
    background-color: rgb(255, 123, 75);
  }
  .options {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
  }

  .right {
    padding: 2px;
    display: grid;
    justify-items: left;
  }

  .left {
    padding: 2px;
    display: grid;
    justify-items: right;
  }

  .grid {
    padding: 0.5em;
    display: grid;
    justify-content: center;
  }

  button {
    min-width: 80px;
  }

  .selected {
    background-color: rgb(255, 123, 75);
  }
</style>
