<script lang="ts">
	export let col: number = 1;
	export let row: number = 2;
	export let space: number = 6;
	export let teacher: string;
	export let subject: string;
	export let groups: string;
	export let time: any;
	export let classroom: string;
	export let onMouseDown: (e: MouseEvent) => void;
	export let onMouseUp: (e: MouseEvent) => void;
	export let touchstart: (e: TouchEvent) => void;
	export let touchend: (e: TouchEvent) => void;
</script>

<li
	on:mousedown={onMouseDown}
	on:mouseup={onMouseUp}
	on:touchstart={touchstart}
	on:touchend={touchend}
	class="mt-px flex"
	data-space={space}
	style={`grid-row: ${row} / span ${space}; grid-column: ${col} / span 1`}
>
	<div
		class="m-1 w-full flex flex-col overflow-y-auto rounded-lg bg-blue-100 p-2 text-xs leading-5 hover:bg-blue-200 hover:text-blue-700"
	>
		<p class="order-1 font-semibold text-blue-700">{subject} - {teacher}</p>
		<p class="order-2 text-blue-500">{groups}</p>
		<p class="brf text-blue-500">
			<time datetime="2022-01-12T06:00">{time[1]}:00</time>
		</p>
	</div>
</li>
