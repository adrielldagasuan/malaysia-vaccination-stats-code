<script>

  export let startDate
  export let endDate
  export let minDate
  export let maxDate

  $: maxStartDate = getDaysFrom(endDate, -2)
  $: minEndDate = getDaysFrom(startDate, 2)

  function handleChangeStart(e) {
    let targetDate = new Date(e.target.value)
    let sDate = new Date(minDate)
    let eDate = new Date(maxStartDate)
    
    if (targetDate > sDate && targetDate < eDate) {
      startDate = e.target.value
    } else if ((targetDate < sDate && targetDate < eDate) || (targetDate.getTime() === sDate.getTime())) {
      startDate = minDate
    } else if ((targetDate > sDate && targetDate > eDate) || (targetDate.getTime() === eDate.getTime())) {
      startDate = maxStartDate
    }
  }

  function handleChangeEnd(e) {
    let targetDate = new Date(e.target.value)
    let sDate = new Date(minEndDate)
    let eDate = new Date(maxDate)

    
    if (targetDate > sDate && targetDate < eDate) {
      endDate = e.target.value
    } else if ((targetDate > sDate && targetDate > eDate) || (targetDate.getTime() === eDate.getTime())) {
      endDate = maxDate
    } else if ((targetDate < sDate && targetDate < eDate) || (targetDate.getTime() === sDate.getTime())) {
      endDate = minEndDate
    } 
  }

  function getDaysFrom(baseDate, distance) {
    let d = new Date(baseDate)
    d.setDate(d.getDate() + distance)
    return formatDate(d)
  }

  function formatDate(date) {
    var d = new Date(date),
        month = '' + (d.getMonth() + 1),
        day = '' + d.getDate(),
        year = d.getFullYear();

    if (month.length < 2) 
        month = '0' + month;
    if (day.length < 2) 
        day = '0' + day;

    return [year, month, day].join('-');
}

</script>


<main>
  <div class="container">
    <div class="options">
      <div class="left">
        <input value={startDate} on:change={handleChangeStart} min={minDate} max={maxStartDate} type="date" />
      </div>
      <div  class="right">
        <input value={endDate} on:change={handleChangeEnd} type="date" min={minEndDate} max={maxDate} />
      </div>
    </div>
  </div>
</main>

<style>

.container{
	display: grid;
	gap: 10px;
	grid-auto-rows: min-content(30px)
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

</style>