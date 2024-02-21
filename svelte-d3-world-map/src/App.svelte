<script>
	export let name = 'World';
	import WorldMap from './WorldMap.svelte';
	let daysCount;
  </script>
  
  <main>
	<h1>WorldWide Covid Data</h1>
	<p>A Visualizaiton of how covid took over the world, here is the data we used <a href="https://www.kaggle.com/datasets/imdevskp/corona-virus-report?resource=download">Covid Data</a>.</p>
	<input type="range" min="0" max="daysCount" value="0" id="timeSlider" step="1">
	<label for="timeSlider" id="sliderLabel">Date: </label>
	<WorldMap/>
  </main>
  
<style>
	@import url('https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=Oswald:wght@200..700&family=Rubik:ital,wght@0,300..900;1,300..900&display=swap');

	main {
		text-align: center;
		padding: 1em;
		width: 100%;
		margin: 0 auto;
		font-family: 'Rubik', sans-serif; /* Change the font-family as needed */
	}

	h1 {
		color: #4b0000;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 3000;
		font-family: 'DM Serif Display', serif; /* Change the font-family as needed */
	}

	p {
		font-family: 'Oswald', sans-serif; /* Change the font-family as needed */
		font-size: 1.3em; /* Adjust the size as needed */
	}

	#timeSlider {
		width: 80%; /* Adjust the width as needed */
		height: 25px; /* Adjust the height as needed */
		-webkit-appearance: none; /* Override default appearance */
		appearance: none;
		outline: none;
		opacity: 0.7;
		transition: opacity 0.2s;
	}
	#timeSlider:hover {
		opacity: 1; /* Fully opaque on hover */
	}
	#timeSlider::-webkit-slider-thumb {
		-webkit-appearance: none;
		appearance: none;
		width: 25px; /* Adjust the thumb width as needed */
		height: 25px; /* Adjust the thumb height as needed */
		background: #33752B; /* Thumb color */
		cursor: pointer;
	}
	#timeSlider::-moz-range-thumb {
		width: 25px; /* Adjust the thumb width as needed */
		height: 25px; /* Adjust the thumb height as needed */
		background: #4CAF50; /* Thumb color */
		cursor: pointer;
	}
</style>
