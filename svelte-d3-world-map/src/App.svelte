<script>
	export let name = 'World';
	import WorldMap from './WorldMap.svelte';
  </script>
  
  <main>
	<h1>WorldWide Covid Data</h1>
	<p>A Visualizaiton of how covid took over the world, here is the data we used <a href="https://www.kaggle.com/datasets/imdevskp/corona-virus-report?resource=download">Covid Data</a>.</p>
	<WorldMap />
  </main>
  
<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 3000;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
