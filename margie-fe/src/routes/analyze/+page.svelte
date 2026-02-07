<script lang="ts">
	import { goto } from '$app/navigation';

	// Use relative URL so it works in both dev and production
	const API_URL = typeof window !== 'undefined' && window.location.hostname === 'localhost'
		? 'http://localhost:8000' // Dev: connect to local backend
		: ''; // Production: use relative URL (same domain)

	let genomePath = $state('');
	let computeLocation = $state<'negishi' | 'local' | 'anvil'>('negishi');
	let loading = $state(false);
	let error = $state('');

	async function handleAnalyze() {
		if (!genomePath.trim()) {
			error = 'Please provide a genome file';
			return;
		}

		try {
			loading = true;
			error = '';

			const response = await fetch(`${API_URL}/v1/ssh/run_margie`, {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json',
				},
				body: JSON.stringify({
					genome_path: genomePath,
				}),
			});

			if (!response.ok) throw new Error('Failed to start analysis');

			const data = await response.json();
			console.log('Analysis started:', data);

			// Redirect to job monitoring page
			if (data.job_id) {
				goto(`/jobs/${data.job_id}`);
			}
		} catch (e) {
			error = e instanceof Error ? e.message : 'Failed to start analysis';
			console.error('Error starting analysis:', e);
		} finally {
			loading = false;
		}
	}
</script>

<div class="container mx-auto p-8 max-w-4xl">
	<h1 class="text-4xl font-bold mb-8 text-center text-primary-500">Genome Analysis</h1>

	{#if error}
		<div class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded mb-4">
			{error}
		</div>
	{/if}

	<!-- Genome Path -->
	<div class="card p-6 bg-surface-100 dark:bg-surface-800 mb-8">
		<h2 class="text-2xl font-semibold mb-4">Genome Path</h2>
		<input
			type="text"
			bind:value={genomePath}
			placeholder="Enter genome file path..."
			class="w-full px-4 py-2 rounded border border-surface-300 dark:border-surface-600 bg-white dark:bg-surface-900"
		/>
	</div>

	<!-- Compute Location and Analyze Button -->
	<div class="card p-6 bg-surface-100 dark:bg-surface-800">
		<button
			type="button"
			onclick={handleAnalyze}
			disabled={loading}
			class="btn variant-filled-primary px-8 py-2"
			color="blue"
		>
			{loading ? 'Starting Analysis...' : 'Analyze'}
		</button>
	</div>
</div>
