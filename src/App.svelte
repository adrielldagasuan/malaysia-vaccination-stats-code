<script>
	import { onMount } from 'svelte';
	import * as _ from 'lodash-es'
	import * as Papa from 'papaparse';
	import Graph from "./Graph.svelte"

	let data;
	let uniqueDates = [];
	let uniqueStates = [];
	let recordsByState;
	let recordsByDate;
	let graphEnabled = "";

	let selectedStat = "Total"
	let selectedDose = 2

	onMount( async () => {
		const res = await fetch('./static/vax_state.csv');
		const csv = await res.text()

		data = Papa.parse(csv, {header: true}).data;

		var objects = _.entries(data).map(r => {
			return r[1]
		}).filter(r => r.date !== "")
		
		recordsByDate = _.groupBy(objects, (r) => {
			if (r.date !== "") {
				return r.date
			}
		})
		uniqueDates = _.keys(recordsByDate)

		recordsByState = _.groupBy(objects, (r) => {
			return r.state
		})

		uniqueStates = _.keys(recordsByState)
	});

	function getDataFromLastNDays(stateName, n) {
		let s = _.values(recordsByState[stateName])
		return _.takeRight(s,n)
	}

	function toggleGraph(stateName) {
		if (graphEnabled === "" || graphEnabled !== stateName) {
			graphEnabled = stateName
		} else if (graphEnabled === stateName) {
			graphEnabled = ""
		}
	}

	function handleDose(dose) {
		selectedDose = dose
	}
	
	function handleStat(stat) {
		selectedStat = stat
	}

</script>

<main>
	<div class="container">
		<div class="center">Malaysia Covid-19 Vaccination Stats</div>
		{#each uniqueStates as state}
			<div class="center">------------------------------------</div>
			<div class="wrapper" on:click={toggleGraph(state)}>
				<div class="grid">{state}</div>
			</div>
			{#if graphEnabled === state}
				<div class="stats">
					<div class="left">
						<button on:click={() => {handleStat('Total')}} class={selectedStat === 'Total' ? 'selected' : ''}>Total</button>
					</div>
					<div class="right">
						<button on:click={() => {handleStat('Daily')}} class= {selectedStat === 'Daily' ? 'selected' : ''}>Daily</button>
					</div>
				</div>
				<div class="stats">
					<div class="left">
						<button on:click={() => {handleDose(2)}} class={selectedDose == 2 ? 'selected' : ''}>Full</button>
					</div>
					<div class="right">
						<button on:click={() => {handleDose(1)}} class={selectedDose == 1 ? 'selected' : ''}>Single</button>
					</div>
				</div>
				<Graph data={getDataFromLastNDays(state,1000)} dose={selectedDose} stat={selectedStat}></Graph>
			{/if}
		{/each}
	</div>
</main>

<style>
.container{
	display: grid;
	gap: 10px;
	grid-auto-rows: min-content(30px)
}
.center {
	display: grid;
  justify-content: center
  
}
.wrapper {
  display: grid;
  gap: 10px;
}
.wrapper:hover {
	background-color: rgb(255, 123, 75)
}
.stats {
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
	background-color: rgb(255, 123, 75)
}


</style>