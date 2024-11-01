<script lang="ts">
	import * as d3 from "d3";
	interface DataObj {
		x: number;
		y: number;
		r: number;
		f: string
	}
	const data = $state<DataObj[]>([]);
	let height = $state(0);
	let width = $state(0);
	let staged = $state<number | undefined>(undefined);
	const yScale = $derived(d3.scaleLinear([0, height]).domain([0, 500]));
	const xScale = $derived(d3.scaleLinear().range([0, width]).domain([0, 1000]));
	function getRandomColor() {
		return "#" + ((1 << 24) * Math.random() | 0).toString(16).padStart(6, "0")
	}
	function handleMouseDown(event: MouseEvent) {
		const newLength = data.push({
			r: 0,
			x: xScale.invert(event.clientX),
			y: yScale.invert(event.clientY),
			f: getRandomColor()
		});
		const self = setInterval(() => {
			const element = data[newLength - 1];
			if (element.r >= 150) {
				clearInterval(self);
			} else element.r+=5;
		}, 50);
		staged = self;
	}
	function handleMouseUp(event: MouseEvent) {
		clearInterval(staged);
		staged = undefined;
	}

</script>

<!-- svelte-ignore a11y_click_events_have_key_events -->
<!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
<main
	bind:clientHeight={height}
	bind:clientWidth={width}
	onmousedown={handleMouseDown}
	onmouseup={handleMouseUp}
>
	<svg {width} {height}>
		{#each data as { x, y, r, f }}
			<circle cx={xScale(x)} cy={yScale(y)} {r} fill={f} />
		{/each}
	</svg>
</main>

<style>
	svg {
		background: #00000E;
	}

	circle {
		opacity: calc(708 / 1000);
	}
	main {
		width: 100%;
		height: 100%;
	}
	circle {
		transition: r 0.5s ease-out;
	}
</style>
