<script lang="ts">
	import { onMount } from 'svelte';

	// Use relative URL so it works in both dev and production
	const API_URL = typeof window !== 'undefined' && window.location.hostname === 'localhost'
		? 'http://localhost' // Dev: connect to local backend
		: ''; // Production: use relative URL (same domain)

	type Entry = {
		value: string;
		timestamp: string;
	};

	let entries: Entry[] = [];
	let newValue = '';
	let loading = false;
	let error = '';

	async function fetchEntries() {
		try {
			loading = true;
			error = '';
			const response = await fetch(`${API_URL}/api/dane/entries`);
			if (!response.ok) throw new Error('Failed to fetch entries');
			const data = await response.json();
			entries = data.entries;
		} catch (e) {
			error = e instanceof Error ? e.message : 'Failed to load entries';
			console.error('Error fetching entries:', e);
		} finally {
			loading = false;
		}
	}

	async function handleSubmit(e: Event) {
		e.preventDefault();
		if (!newValue.trim()) return;

		try {
			loading = true;
			error = '';
			const response = await fetch(`${API_URL}/api/dane/entries`, {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json',
				},
				body: JSON.stringify({ value: newValue }),
			});

			if (!response.ok) throw new Error('Failed to submit entry');

			newValue = '';
			await fetchEntries();
		} catch (e) {
			error = e instanceof Error ? e.message : 'Failed to submit entry';
			console.error('Error submitting entry:', e);
		} finally {
			loading = false;
		}
	}

	onMount(() => {
		fetchEntries();
	});
</script>

<div class="container mx-auto p-8 max-w-2xl">
	<h1 class="text-4xl font-bold mb-8 text-center text-primary-500">Hi Dane</h1>

	{#if error}
		<div class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded mb-4">
			{error}
		</div>
	{/if}

	<div class="card p-6 bg-surface-100 dark:bg-surface-800 mb-8">
		<form on:submit={handleSubmit} class="flex gap-4">
			<input
				type="text"
				bind:value={newValue}
				placeholder="Enter a value..."
				required
				disabled={loading}
				class="flex-1 px-4 py-2 rounded border border-surface-300 dark:border-surface-600 bg-white dark:bg-surface-900"
			/>
			<button
				type="submit"
				disabled={loading || !newValue.trim()}
				class="btn variant-filled-primary px-6"
			>
				{loading ? 'Submitting...' : 'Submit'}
			</button>
		</form>
	</div>

	<div class="card p-6 bg-surface-100 dark:bg-surface-800">
		<h2 class="text-2xl font-semibold mb-4">Saved Entries</h2>

		{#if loading && entries.length === 0}
			<p class="text-surface-600 dark:text-surface-400">Loading...</p>
		{:else if entries.length === 0}
			<p class="text-surface-600 dark:text-surface-400">No entries yet</p>
		{:else}
			<ul class="space-y-2">
				{#each entries as entry}
					<li class="p-3 bg-surface-200 dark:bg-surface-700 rounded flex justify-between items-center">
						<span>{entry.value}</span>
						<span class="text-sm text-surface-500">{entry.timestamp}</span>
					</li>
				{/each}
			</ul>
		{/if}
	</div>
</div>
