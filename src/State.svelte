<script>
  import Graph from "./Graph.svelte";
  import DateSelect from "./DateSelect.svelte";
  import _ from "lodash-es";
  export let allData;

  let data = allData;

  let minDate = data[0].date;
  let maxDate = _.last(data).date;

  let graphName;

  let startDate = minDate;
  let endDate = maxDate;

  let selectedDose = 2;
  let selectedStat = "Total";

  let getX = (obj) => {
    return obj.date;
  };
  let getY;

  $: if (startDate || endDate) {
    let startIndex = _.findIndex(allData, (r) => {
      return r.date === startDate;
    });

    let endIndex = _.findIndex(allData, (r) => {
      return r.date === endDate;
    });

    data = _.slice(allData, startIndex, endIndex + 1);
  }

  $: if (selectedDose || selectedStat) {
    if (selectedStat === "Total") {
      if (selectedDose == 1) {
        getY = getTotal1Dose;
        graphName = "Total numbers for 1 Dose";
      } else if (selectedDose == 2) {
        graphName = "Total numbers for 2 Doses";
        getY = getTotal2Doses;
      }
    } else if (selectedStat === "Daily") {
      if (selectedDose == 1) {
        graphName = "Daily numbers for 1 Dose";
        getY = getDaily1Dose;
      } else if (selectedDose == 2) {
        graphName = "Daily numbers for 2 Doses";
        getY = getDaily2Doses;
      }
    }
  }

  function handleDose(dose) {
    selectedDose = dose;
  }

  function handleStat(stat) {
    selectedStat = stat;
  }

  function getTotal2Doses(obj) {
    return parseInt(obj.dose2_cumul);
  }

  function getTotal1Dose(obj) {
    return parseInt(obj.dose1_cumul);
  }

  function getDaily2Doses(obj) {
    return parseInt(obj.dose2_daily);
  }

  function getDaily1Dose(obj) {
    return parseInt(obj.dose1_daily);
  }
</script>

<main>
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
  <Graph {data} {graphName} {getX} {getY} />
</main>

<style>
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

  button {
    min-width: 80px;
  }

  .selected {
    background-color: rgb(255, 123, 75);
  }
</style>
