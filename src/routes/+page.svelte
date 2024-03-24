<script lang="ts">
	import Sun from 'svelte-radix/Sun.svelte';
	import Moon from 'svelte-radix/Moon.svelte';
	import HamburgerMenu from 'svelte-radix/HamburgerMenu.svelte';
	import GithubLogo from 'svelte-radix/GithubLogo.svelte';
	import * as Sheet from '$lib/components/ui/sheet';
	import * as Command from '$lib/components/ui/command';
	import { Button } from '$lib/components/ui/button';
	import { toggleMode } from 'mode-watcher';
	import { Calendar } from '$lib/components';

	let search = '';
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
	<Calendar />
</main>

<style>
	.hide-scroll {
		scrollbar-width: none;
	}
</style>
