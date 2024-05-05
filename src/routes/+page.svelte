<script lang="ts">
	import Sun from 'svelte-radix/Sun.svelte';
	import Moon from 'svelte-radix/Moon.svelte';
	import HamburgerMenu from 'svelte-radix/HamburgerMenu.svelte';
	import GithubLogo from 'svelte-radix/GithubLogo.svelte';
	import * as Sheet from '$lib/components/ui/sheet';
	import * as Command from '$lib/components/ui/command';
	import { Button } from '$lib/components/ui/button';
	import { Input } from '$lib/components/ui/input';
	import { toggleMode } from 'mode-watcher';
	import { Calendar } from '$lib/components';

	let search = '';
	let id = '';
	let tt = [];
	let loaded = false;

	const uploadFile = async () => {
		const input = document.getElementById('upload') as HTMLInputElement;
		const button = document.querySelector('button[for="upload"]') as HTMLButtonElement;
		if (input.files && input.files.length > 0) {
			const file = input.files[0];
			input.disabled = true;
			button.disabled = true;

			const contents = JSON.parse(await file.text());

			let resp = fetch('http://localhost:8080/tt/new', {
				method: 'POST',
				body: JSON.stringify({ data: contents })
			})
				.then((res) => res.json())
				.then((data) => {
					console.log(data);
					input.disabled = false;
					button.disabled = false;
					id = data['success'];
				})
				.catch((err) => {
					console.error(err);
					input.disabled = false;
					button.disabled = false;
				});
		}
	};

	const formatTimeTable = (data) => {
		let lines = data.split('\n');
		lines = lines.slice(16);
		let classes = lines.reduce((res, item, index) => {
			const chunkIndex = Math.floor(index / 9);

			if (!res[chunkIndex]) {
				res[chunkIndex] = []; // start a new chunk
			}

			res[chunkIndex].push(item);
			return res;
		}, []);
		let cls = [];
		for (const cl of classes) {
			let s = {
				teacher: cl[2].split(':')[1].trim(),
				subject: cl[3].split(':')[1].trim(),
				groups: cl[4].split(':')[1].trim(),
				duration: cl[6].split(':')[1].trim().split(' ')[0],
				classroom: cl[7].split(':')[1].trim(),
				time: cl[8].split(':')[1].trim().split(' ').slice(0, 2)
			};
			cls.push(s);
		}
		return cls;
	};

	let status_update = () => {
		let resp = fetch(`http://localhost:8080/tt/status/${id}`)
			.then((res) => res.json())
			.then((data) => {
				console.log(data);
				let status = data['status'];
				if (status == 'processing') {
					setTimeout(status_update, 2000);
					return;
				}
				tt = formatTimeTable(status);
				loaded = true;
			})
			.catch((err) => {
				console.error(err);
			});
	};

	$: {
		if (id != '' && !loaded) {
			status_update();
		}
	}
</script>

<main class="flex flex-row h-screen">
	<div class="w-1/4 h-full flex-col flex border-r">
		<div class="p-4 flex flex-row items-center">
			<Sheet.Root>
				<Sheet.Trigger><HamburgerMenu /></Sheet.Trigger>
				<Sheet.Content>
					<Sheet.Header>
						<Sheet.Title>Are you sure absolutely sure?</Sheet.Title>
						<Sheet.Description>
							This action cannot be undone. This will permanently delete your account and remove
							your data from our servers.
						</Sheet.Description>
					</Sheet.Header>
				</Sheet.Content>
			</Sheet.Root>
			<span class="ml-4 text-3xl font-bold">TimeWeaver</span>
		</div>
		<div class="flex flex-col p-4 h-full overflow-y-scroll hide-scroll">
			<Command.Root
				class="mx-4 w-auto h-min bg-accent text-accent-foreground rounded-lg rounded-t-2xl border"
			>
				<Command.Input placeholder="Search..." bind:value={search} />
				{#if search != ''}
					<Command.List>
						<Command.Empty>No results found.</Command.Empty>
						<Command.Group heading="Suggestions">
							<Command.Item>
								<span>Calendar</span>
							</Command.Item>
							<Command.Item>
								<span>Search Emoji</span>
							</Command.Item>
							<Command.Item>
								<span>Launch</span>
							</Command.Item>
						</Command.Group>
						<Command.Separator />
						<Command.Group heading="Settings">
							<Command.Item>
								<span>Profile</span>
							</Command.Item>
							<Command.Item>
								<span>Mail</span>
							</Command.Item>
							<Command.Item>
								<span>Settings</span>
							</Command.Item>
						</Command.Group>
					</Command.List>
				{/if}
			</Command.Root>
			<div
				class="mx-4 mt-2 p-3 w-auto grid grid-cols-2 bg-secondary text-secondary-foreground rounded-lg rounded-b-2xl border"
			>
				<span class="text-lg font-bold">B9</span>
				<span class="text-lg font-bold justify-self-end">2021</span>
				<span class="text-md">Single Batch</span>
			</div>
			<div class="mx-4 mt-4 w-auto flex flex-row gap-2">
				<Input id="upload" type="file" />
				<Button on:click={uploadFile} for="upload">Upload</Button>
			</div>
		</div>
		<div class="mt-auto p-4 flex flex-row gap-2 items-center">
			<Button on:click={toggleMode} variant="ghost" size="icon">
				<Sun class="h-5 w-5 rotate-0 scale-100 transition-all dark:-rotate-90 dark:scale-0" />
				<Moon
					class="absolute h-5 w-5 rotate-90 scale-0 transition-all dark:rotate-0 dark:scale-100"
				/>
				<span class="sr-only">Toggle theme</span>
			</Button>
			<Button href="https://github.com/149segolte" variant="ghost" size="icon">
				<GithubLogo class="h-5 w-5" />
			</Button>
		</div>
	</div>
	<Calendar classes={tt} />
</main>

<style>
	.hide-scroll {
		scrollbar-width: none;
	}
</style>
