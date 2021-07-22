<script>
	import { onMount } from 'svelte';
	import * as _ from 'lodash-es'
	import * as Papa from 'papaparse';
	import State from './State.svelte'

	let data;
	let uniqueDates = [];
	let uniqueStates = [];
	let recordsByState;
	let recordsByDate;

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

	function getDataForState(state) {
		return _.values(recordsByState[state])
	}

</script>

<main>
	<div class="container">
		<div class="center">Malaysia Covid-19 Vaccination Stats</div>
		{#each uniqueStates as state}
			<State allData={getDataForState(state)} state={state}/>
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


</style>