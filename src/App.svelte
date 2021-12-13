<script>
	import Plot from './Plot.svelte';

	/*
	const points = [
		{ received: 1639169090816, code: 10 },
		{ received: 1639169091016, code: 20 },
		{ received: 1639169091216, code: 40 },
		{ received: 1639169091416, code: 80 }
	]
	*/

	let points = [];

	let counter = 200;
	const delay = 200;

	setTimeout(function update() {
		points = [...points, {
			received: Date.now(),
			code: counter
		}];

		if( counter < 2000 ) {
			counter += 20;
			setTimeout(update, delay);
		}
	}, delay);
</script>

<Plot {points}/>

<style>

</style>