<script>
	export let locations;
	// import 'dotenv/config';

	import { onMount } from 'svelte';
	import mapboxgl from 'mapbox-gl';

    import 'dotenv'

	let map;
	$: isMobile = false;

	onMount(() => {
		window.innerWidth <= 490 ? (isMobile = 'sm') : (isMobile = 'md');

		mapboxgl.accessToken = import.meta.env.VITE_MAPBOX;

		map = new mapboxgl.Map({
			container: 'map',
			style: 'mapbox://styles/mapbox/dark-v11',
			center: selectedLocation.coordinates,
			zoom: 7,
			attribution: ``,
			// maxNativeZoom: 21,
			maxZoom: 20
			// subdomains: ['a', 'b', 'c', 'd', 'e']
		});

		map.on('load', () => {
			map.loadImage('', (error, image) => {
				// if (error) throw error;

				// map.addImage('custom-marker', image);

				locations.forEach((point) => {
					const customIcon = document.createElement('div');
					customIcon.className = 'custom-marker';

					new mapboxgl.Marker()
						.setLngLat(point.coordinates)
						.addTo(map)
						.getElement()
						.addEventListener('click', () => {
							handleMarkerClick(point);
						});
				});
			});
		});
	});

	function handleMarkerClick(point) {
		selectedLocation = point;
		shortDesc = `${selectedLocation.desc.slice(0, 130)}...`;

		selectedLocation.title.length < 20
			? (textTitle = 'text-xl')
			: selectedLocation.title.length > 20 && selectedLocation.title.length < 50
			? (textTitle = 'text-base')
			: (textTitle = 'text-sm');

		map.flyTo({
			center: point.coordinates,
			zoom: 12,
			essential: true
		});
	}

	$: iconSize = [40, 40];
	let radius = 1000;

	$: textTitle = 'text-xl';

	$: selectedLocation = {
		title: '',
		desc: `Lorem ipsum is placeholder text commonly used in the graphic, print, and publishing industries for previewing layouts and visual mockups.`,
		coordinates: ['27.561879', '53.902334'],
		link: '/articles/fuck-the-strapicms',
		iconUrl: ''
	};

	let showLocationIndex = -1;
	let showFull = false;

	$: shortDesc = 'Tap on any point on the map.';
</script>

<!-- <section class='max-h-[416px] rounded-tl-[50px] rounded-tr-[50px] border-[12px] border-[#ffffff1f]'> -->
<section class="flex min-h-screen  flex-col md:flex-col lg:flex-row">
	<div
		id="map"
		class="rounded-tl-[50px] rounded-tr-[50px] {isMobile == 'sm' && showFull
			? 'hidden'
			: ''} z-2 h-full w-full md:h-screen  lg:h-screen lg:w-full"
	>
		<div class="absolute top-8 left-4 h-auto max-w-sm">
			<h1
				class="font-oswald-bold z-10 mx-4 max-w-[230px] text-xl uppercase leading-snug text-neutral-100"
			>
				Mapa zabytków w Gdańsku
			</h1>
		</div>
		<!-- h-[656px] -->
	</div>

	<div class="flex h-auto w-full flex-col bg-neutral-200 lg:h-screen lg:w-[510px] ">
		{#if !showFull}
			<div
				class="  max-h-[300px] w-full bg-neutral-100 px-8 pt-10 text-neutral-900 shadow-2xl  md:px-4  lg:px-4"
			>
				<h3 class="font-oswald-normal  mb-4 {textTitle} text-neutral-900">
					{selectedLocation.title}
				</h3>
				<p
					class="font-notoSans-normal text-dimInHuj  mb-2 h-auto {selectedLocation.desc.length > 581
						? ''
						: 'limited-text'} text-neutral-900"
				>
					{selectedLocation.desc}
				</p>

				<div class="flex flex-row flex-wrap {selectedLocation.title.length ? 'mb-8' : 'pb-4'}  ">
					{#if selectedLocation.title.length}
						<button
							class="mb-6 flex h-12 w-full flex-row items-center justify-center rounded-md border-2 border-neutral-900 py-2 px-4 text-center text-sm text-neutral-900 "
							on:click={() => {
								showFull = !showFull;
							}}
						>
							<img src="./images/map/down_arow.svg" class="mr-2 h-4 w-4" alt="" />

							Развернуть</button
						>
					{/if}
				</div>
			</div>
			<div
				class="list points h-full]  flex max-w-sm flex-col items-center overflow-y-scroll bg-neutral-300 px-6 py-4 px-4 py-2 md:h-full"
			>
				{#each locations as location, i}
					<div
						on:click={() => {
							handleMarkerClick(location);
							showLocationIndex = i;
						}}
						class="  mx-auto mb-1 flex w-full flex-row  items-center     {showLocationIndex == i
							? 'rounded-md  border-b-2 border-gray-700 bg-rose-700'
							: 'delay-550 duration-600 hover:scale-140 border-b-2 border-solid border-gray-700  bg-neutral-300 transition ease-in-out hover:-translate-y-1 hover:rounded-md  hover:bg-white   hover:shadow-lg'}  py-5 px-4  "
					>
						<img
							src="./images/map/fire.svg"
							class="mr-2"
							style="width: 18px; height: 18px;"
							alt=""
						/>
						<h3 class="font-notoSans-normal max-w-sm text-sm text-neutral-900">{location.title}</h3>
					</div>
				{/each}
			</div>
		{:else}
			<div class=" h-screen w-full bg-neutral-100  px-8 pt-8  text-neutral-900 shadow-2xl">
				<div class="mb-2 flex justify-end">
					<button
						on:click={() => {
							showFull = !showFull;
						}}
						class="btn-outline btn-circle btn"
					>
						<svg
							xmlns="http://www.w3.org/2000/svg"
							class="h-6 w-6"
							fill="none"
							viewBox="0 0 24 24"
							stroke="currentColor"
							><path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M6 18L18 6M6 6l12 12"
							/></svg
						>
					</button>
				</div>
				<h3 class="font-oswald-normal  mb-4 {textTitle} text-neutral-900">
					{selectedLocation.title}
				</h3>
				<p
					class="font-notoSans-normal text-dimInHuj  mb-2 h-full h-auto overflow-y-auto text-neutral-900"
				>
					{selectedLocation.desc}
				</p>
			</div>
		{/if}
	</div>
</section>

<style>
	.custom-marker {
		background-image: url('images/map/pointer.svg');
		background-size: cover;
		width: 50px;
		height: 50px;
		border-radius: 50%;
		cursor: pointer;
	}

	#map {
		height: 100vh;
		width: 100%;
	}

	@media screen and (max-width: 450px) {
		#map {
			max-height: 656px;
			overflow: hidden;
			width: 100%;
		}
	}

	@media screen and (max-width: 1000px) {
		.list {
			display: none;
		}

		.points {
			max-height: 100%;
		}
	}

	.limited-text {
		display: -webkit-box;
		-webkit-box-orient: vertical;
		overflow: hidden;
		-webkit-line-clamp: 5;
	}
</style>
