<script>
	import Banner from '$components/Banner.svelte'
	import Side from '$components/Side.svelte'
	import Timeline from '$components/Timeline.svelte'
	import MainTable from '$components/MainTable.svelte'
	import MainTable2 from '$components/MainTable2.svelte'
import Crossfilter from '../components/query/Crossfilter.svelte'
</script>

<style lang="scss">

	$eigenlicht-color: #16161d;
	:root {
		font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
		background-color: $eigenlicht-color;
	}
	
	main {
		text-align: center;
		padding: 1em;
		margin: 0 auto;
	}
	@media (min-width: 480px) {
		h1 {
			max-width: none;
		}

		p {
			max-width: none;
		}
	}
</style>

<Banner></Banner>
<Crossfilter db='overwatch' collection='processed'>
	<Timeline></Timeline>
	<MainTable2></MainTable2>
</Crossfilter>
<!-- <Side></Side> -->

<main>
</main>

