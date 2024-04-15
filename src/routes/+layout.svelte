<script>
	// import AOS from 'aos';/

	// import 'aos/dist/aos.css';
	import '../leafletCustom.css';
	import '../mapbox.css';

	import { onMount } from 'svelte';
	import '../app.css';
	onMount(() => {
		// AOS.init();
	});
</script>

<main class="">
	<slot />
</main>

<style>
	
</style>
