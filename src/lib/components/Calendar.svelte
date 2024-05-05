<script lang="ts">
	export let classes;

	const day = {
		Monday: 1,
		Tuesday: 2,
		Wednesday: 3,
		Thursday: 4,
		Friday: 5,
		Saturday: 6,
		Sunday: 7
	};

	$: {
		console.log(classes);
	}

	import { Badge } from '$lib/components/ui/badge';
	import { Event } from '$lib/components';
	import { onMount } from 'svelte';

	onMount(() => {
		let myElement = document.getElementById('scrollHead');
		let topPos = myElement.offsetTop;
		document.getElementById('scrollBody').scrollTop = topPos;
	});

	let bgMover: HTMLElement | null = null;

	function handle(X: number, Y: number) {
		let box = document.getElementById('box');
		let parent = null;
		let space = 6;
		if (bgMover) {
			parent = bgMover.parentElement;
			space = parseInt(parent.getAttribute('data-space'));
			parent.style.borderRadius = '0px';
		}
		if (parent && box) {
			let width = box.getBoundingClientRect().width - 30;
			let height = box.getBoundingClientRect().height - 28;
			let x = X - box.getBoundingClientRect().left;
			let y = Y - box.getBoundingClientRect().top - 28;
			parent.style.gridColumn = (Math.floor((x / width) * 7) + 1).toString() + ' / span 1';
			parent.style.gridRow =
				(Math.floor((y / height) * 48) * 6 + 2).toString() + ` / span ${space}`;
		}
	}

	function handleEnd() {
		if (bgMover) {
			bgMover.parentElement.style.borderRadius = '0.375rem';
			bgMover = null;
		}
	}

	function touchEvents() {
		let x = 0;
		let y = 0;
		let timer: number | null = null;
		let touchduration = 300; //length of time we want the user to touch before we do something

		let onlongtouch = function () {
			timer = null;
			handle(x, y);
		};

		let touchstart = function (event: any) {
			event.preventDefault();
			x = event.touches[0].clientX;
			y = event.touches[0].clientY;
			bgMover = event.target;
			if (!timer) {
				timer = setTimeout(onlongtouch, touchduration);
			}
		};

		let touchend = function () {
			handleEnd();
			//stops short touches from firing the event
			if (timer) {
				clearTimeout(timer);
				timer = null;
			}
		};

		return { touchstart, touchend };
	}

	function mouseEvents() {
		let move = false;

		let onMouseMove = function (event: any) {
			if (move) {
				handle(event.clientX, event.clientY);
			}
		};

		let onMouseDown = function (event: any) {
			event.preventDefault();
			move = true;
			bgMover = event.target;
		};

		let onMouseUp = function () {
			move = false;
			handleEnd();
		};

		return { onMouseMove, onMouseDown, onMouseUp };
	}

	let { touchstart, touchend } = touchEvents();
	let { onMouseMove, onMouseDown, onMouseUp } = mouseEvents();
</script>

<div class="w-full h-full bg-gray-50">
	<div class="flex h-full flex-col">
		<div
			id="scrollBody"
			class="scroll-smooth isolate flex flex-auto flex-col overflow-auto bg-white"
		>
			<div
				class="relative h-max flex max-w-full flex-none flex-col sm:max-w-none md:max-w-full"
				style="width: 165%;"
			>
				<div class="sticky top-0 z-30 flex-none bg-transparent sm:pr-8 pointer-events-none">
					<div
						class="-mr-px grid grid-cols-7 border-r border-gray-100 text-sm leading-6 text-gray-500"
					>
						<div class="col-end-1 w-14"></div>
						<div class="flex items-center justify-end py-3 mr-3">
							<Badge>Mon</Badge>
						</div>
						<div class="flex items-center justify-end py-3 mr-3">
							<Badge>Tue</Badge>
						</div>
						<div class="flex items-center justify-end py-3 mr-3">
							<Badge>Wed</Badge>
						</div>
						<div class="flex items-center justify-end py-3 mr-3">
							<Badge>Thu</Badge>
						</div>
						<div class="flex items-center justify-end py-3 mr-3">
							<Badge>Fri</Badge>
						</div>
						<div class="flex items-center justify-end py-3 mr-3">
							<Badge>Sat</Badge>
						</div>
						<div class="flex items-center justify-end py-3 mr-3">
							<Badge>Sun</Badge>
						</div>
					</div>
				</div>
				<div class="w-full h-[160rem] -z-10"></div>
				<div class="absolute w-full top-0 flex flex-auto">
					<div
						id="edge"
						class="sticky left-0 z-10 w-14 flex-none bg-white ring-1 ring-gray-100"
					></div>
					<div class="grid flex-auto grid-cols-1 grid-rows-1">
						<div
							class="col-start-1 col-end-2 row-start-1 grid divide-y divide-gray-100"
							style="grid-template-rows: repeat(48, minmax(3.5rem, 1fr));"
						>
							<div class="row-end-1 h-7"></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									12AM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									1AM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									2AM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									3AM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									4AM
								</div>
							</div>
							<div></div>
							<div>
								<div
									id="scrollHead"
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									5AM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									6AM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									7AM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									8AM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									9AM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									10AM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									11AM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									12PM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									1PM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									2PM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									3PM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									4PM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									5PM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									6PM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									7PM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									8PM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									9PM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									10PM
								</div>
							</div>
							<div></div>
							<div>
								<div
									class="sticky left-0 z-20 -ml-14 -mt-2.5 w-14 pr-8 text-right text-xs leading-5 text-gray-400"
								>
									11PM
								</div>
							</div>
							<div></div>
						</div>
						<div
							class="col-start-1 col-end-2 row-start-1 hidden grid-cols-7 grid-rows-1 divide-x divide-gray-100 sm:grid sm:grid-cols-7"
						>
							<div class="col-start-1 row-span-full"></div>
							<div class="col-start-2 row-span-full"></div>
							<div class="col-start-3 row-span-full"></div>
							<div class="col-start-4 row-span-full"></div>
							<div class="col-start-5 row-span-full"></div>
							<div class="col-start-6 row-span-full"></div>
							<div class="col-start-7 row-span-full"></div>
							<div class="col-start-8 row-span-full w-8"></div>
						</div>
						<ol
							id="box"
							on:mousemove={onMouseMove}
							class="select-none col-start-1 col-end-2 row-start-1 grid grid-cols-1 sm:grid-cols-7 sm:pr-8"
							style="grid-template-rows: 1.75rem repeat(288, minmax(0px, 1fr)) auto;"
						>
							{#each classes as cl}
								<Event
									col={day[cl.time[0]]}
									row={(cl.time[1] - 9) * 12 + 110}
									space={cl.duration * 12}
									teacher={cl.teacher}
									subject={cl.subject}
									groups={cl.groups}
									time={cl.time}
									classroom={cl.classroom}
									{onMouseDown}
									{onMouseUp}
									{touchstart}
									{touchend}
								/>
							{/each}
						</ol>
					</div>
				</div>
			</div>
		</div>
	</div>
</div>
