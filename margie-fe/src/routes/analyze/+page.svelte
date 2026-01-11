<script lang="ts">
	// Use relative URL so it works in both dev and production
	const API_URL = typeof window !== 'undefined' && window.location.hostname === 'localhost'
		? 'http://localhost:8000' // Dev: connect to local backend
		: ''; // Production: use relative URL (same domain)

	let genomePath = $state('');
	let isDragging = $state(false);
	let computeLocation = $state<'negishi' | 'local' | 'anvil'>('negishi');
	let loading = $state(false);
	let error = $state('');

	// Analysis programs
	let selectedPrograms = $state({
		prodigal: false,
		tigrfam: false,
		kegg: false,
		cazyme: false,
		rast: false,
		cog: false,
		pfam: false,
		custom: false
	});

	function handleDragOver(e: DragEvent) {
		e.preventDefault();
		isDragging = true;
	}

	function handleDragLeave(e: DragEvent) {
		e.preventDefault();
		isDragging = false;
	}

	function handleDrop(e: DragEvent) {
		e.preventDefault();
		isDragging = false;

		const items = e.dataTransfer?.items;
		if (items) {
			for (let i = 0; i < items.length; i++) {
				if (items[i].kind === 'file') {
					const file = items[i].getAsFile();
					if (file) {
						genomePath = file.name;
					}
				}
			}
		}
	}

	async function handleAnalyze() {
		if (!genomePath.trim()) {
			error = 'Please provide a genome file';
			return;
		}

		const selectedProgramsList = Object.entries(selectedPrograms)
			.filter(([_, selected]) => selected)
			.map(([program, _]) => program);

		if (selectedProgramsList.length === 0) {
			error = 'Please select at least one analysis program';
			return;
		}

		try {
			loading = true;
			error = '';

			// TODO: Replace with your actual API endpoint
			const response = await fetch(`${API_URL}/v1/analyze`, {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json',
				},
				body: JSON.stringify({
					genome_path: genomePath,
					programs: selectedProgramsList,
					compute_location: computeLocation
				}),
			});

			if (!response.ok) throw new Error('Failed to start analysis');

			const data = await response.json();
			console.log('Analysis started:', data);

			// Reset form after successful submission
			genomePath = '';
			selectedPrograms = {
				prodigal: false,
				tigrfam: false,
				kegg: false,
				cazyme: false,
				rast: false,
				cog: false,
				pfam: false,
				custom: false
			};
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

	<!-- Drag and Drop Zone -->
	<div class="card p-6 bg-surface-100 dark:bg-surface-800 mb-8">
		<h2 class="text-2xl font-semibold mb-4">Upload Genome</h2>
		<div
			class="border-2 border-dashed rounded-lg p-12 text-center transition-colors {isDragging
				? 'border-primary-500 bg-primary-100 dark:bg-primary-900/20'
				: 'border-surface-300 dark:border-surface-600'}"
			ondragover={handleDragOver}
			ondragleave={handleDragLeave}
			ondrop={handleDrop}
			role="button"
			tabindex="0"
		>
			{#if genomePath}
				<div class="text-lg font-mono text-primary-500">
					{genomePath}
				</div>
			{:else}
				<div class="text-surface-600 dark:text-surface-400">
					<p class="text-xl mb-2">Drag and drop genome file here</p>
					<p class="text-sm">or enter path below</p>
				</div>
			{/if}
		</div>

		<input
			type="text"
			bind:value={genomePath}
			placeholder="Or enter genome file path..."
			class="w-full px-4 py-2 rounded border border-surface-300 dark:border-surface-600 bg-white dark:bg-surface-900"
		/>
	</div>

	<!-- Analysis Programs -->
	<div class="card p-6 bg-surface-100 dark:bg-surface-800 mb-8">
		<h2 class="text-2xl font-semibold mb-4">Select Analysis Programs</h2>
		<div class="grid grid-cols-2 md:grid-cols-4 gap-4">
			<label class="flex items-center space-x-2 cursor-pointer">
				<input type="checkbox" bind:checked={selectedPrograms.prodigal} class="checkbox" />
				<span>Prodigal</span>
			</label>
			<label class="flex items-center space-x-2 cursor-pointer">
				<input type="checkbox" bind:checked={selectedPrograms.tigrfam} class="checkbox" />
				<span>TigrFam</span>
			</label>
			<label class="flex items-center space-x-2 cursor-pointer">
				<input type="checkbox" bind:checked={selectedPrograms.kegg} class="checkbox" />
				<span>Kegg</span>
			</label>
			<label class="flex items-center space-x-2 cursor-pointer">
				<input type="checkbox" bind:checked={selectedPrograms.cazyme} class="checkbox" />
				<span>Cazyme</span>
			</label>
			<label class="flex items-center space-x-2 cursor-pointer">
				<input type="checkbox" bind:checked={selectedPrograms.rast} class="checkbox" />
				<span>Rast</span>
			</label>
			<label class="flex items-center space-x-2 cursor-pointer">
				<input type="checkbox" bind:checked={selectedPrograms.cog} class="checkbox" />
				<span>COG</span>
			</label>
			<label class="flex items-center space-x-2 cursor-pointer">
				<input type="checkbox" bind:checked={selectedPrograms.pfam} class="checkbox" />
				<span>Pfam</span>
			</label>
			<label class="flex items-center space-x-2 cursor-pointer">
				<input type="checkbox" bind:checked={selectedPrograms.custom} class="checkbox" />
				<span>Custom</span>
			</label>
		</div>
	</div>

	<!-- Compute Location and Analyze Button -->
	<div class="card p-6 bg-surface-100 dark:bg-surface-800">
		<h2 class="text-2xl font-semibold mb-4">Compute Location</h2>
		<div class="flex gap-4 items-center">
			<select
				bind:value={computeLocation}
				class="px-4 py-2 rounded border border-surface-300 dark:border-surface-600 bg-white dark:bg-surface-900"
			>
				<option value="negishi">Negishi (lindems)</option>
				<option value="local">Local</option>
				<option value="anvil">Anvil</option>
			</select>

			<button
				type="button"
				onclick={handleAnalyze}
				disabled={loading}
				class="btn variant-filled-primary px-8 py-2"
			>
				{loading ? 'Starting Analysis...' : 'Analyze'}
			</button>
		</div>
	</div>
</div>
