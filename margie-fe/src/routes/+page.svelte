<script lang="ts">
    import { goto } from "$app/navigation";
	import Navibar from "$lib/Navibar.svelte";
	import { Upload } from 'lucide-svelte';

	let files: FileList | undefined;
	let isDragging = false;

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
		files = e.dataTransfer?.files;
	}

	function handleFileSelect(e: Event) {
		const target = e.target as HTMLInputElement;
		files = target.files ?? undefined;
	}

	function handleSubmit() {
		// TODO: API call to /api/genomes/submit
		console.log('Submitting genome files:', files);
		alert('Genome submission would happen here!');
	}

	function handleViewResults() {
		// TODO: Navigate to /results
		console.log('Navigate to results page');
		alert('Would navigate to /results');
	}

	function handleViewHistory() {
		// TODO: Navigate to /history
		console.log('Navigate to history page');
		alert('Would navigate to /history');
	}
</script>

<Navibar/>

<!-- Under Construction Banner -->
<div class="bg-warning-500/20 border-y-4 border-warning-500 py-4">
	<div class="container mx-auto px-4 flex items-center justify-center gap-3">
		<span class="text-2xl">🚧</span>
		<p class="text-lg font-semibold text-warning-700 dark:text-warning-300">Under Construction - all buttons are disabled</p>
		<span class="text-2xl">🚧</span>
	</div>
</div>

<div class="container mx-auto p-8 space-y-8">
	<!-- Hero Section -->
	<section class="text-center py-12">
		<h1 class="text-5xl font-bold text-primary-500 mb-4">MARGIE</h1>
		<p class="text-2xl text-secondary-500 font-semibold mb-2">Mostly Automated & Rapid Genome Intelligence Engine</p>
		<p class="text-lg text-surface-600 dark:text-surface-300 mb-8">Upload your genome files for rapid analysis and insights</p>
	</section>

	<!-- File Upload Section -->
	<section class="max-w-4xl mx-auto">
		<div class="card p-8 bg-surface-100 dark:bg-surface-800">
			<h2 class="text-2xl font-bold mb-6 text-center">Upload Genome Files</h2>

			<!-- Drag & Drop Zone -->
			<div
				class="border-4 border-dashed rounded-lg p-12 text-center transition-colors {isDragging ? 'border-primary-500 bg-primary-100 dark:bg-primary-900/20' : 'border-surface-400 dark:border-surface-600'}"
				on:dragover={handleDragOver}
				on:dragleave={handleDragLeave}
				on:drop={handleDrop}
			>
				<Upload class="mx-auto mb-4 size-16 text-surface-500" />
				<p class="text-xl mb-2 font-semibold">Drag & Drop Genome Files Here</p>
				<p class="text-surface-600 dark:text-surface-400 mb-4">or</p>
				<label class="btn variant-filled-secondary cursor-pointer">
					<input
						type="file"
						multiple
						accept=".fasta,.fa,.fastq,.fq,.gb,.gbk"
						class="hidden"
						on:change={handleFileSelect}
					/>
					Browse Files
				</label>
				<p class="text-sm text-surface-500 mt-4">Supported formats: FASTA, FASTQ, GenBank</p>
			</div>

			<!-- Selected Files Display -->
			{#if files && files.length > 0}
				<div class="mt-6">
					<h3 class="font-semibold mb-3">Selected Files ({files.length}):</h3>
					<div class="space-y-2">
						{#each Array.from(files) as file}
							<div class="flex items-center justify-between p-3 bg-surface-200 dark:bg-surface-700 rounded">
								<span class="text-sm">{file.name}</span>
								<span class="badge variant-filled-tertiary text-xs">{(file.size / 1024).toFixed(2)} KB</span>
							</div>
						{/each}
					</div>
				</div>
			{/if}

			<!-- Submit Button -->
			<div class="mt-6 text-center text-primary-600">
				<button
					class="btn variant-filled-primary btn-lg"
					disabled={!files || files.length === 0}
					on:click={handleSubmit}
				>
					Submit Genome
				</button>
			</div>
		</div>
	</section>

	<!-- Analysis Tools Section -->
	<section>
		<h2 class="text-3xl font-bold text-center mb-8">Analysis Tools</h2>
		<div class="grid grid-cols-1 md:grid-cols-2 gap-6 max-w-4xl mx-auto">
			<button class="card p-6 bg-surface-100 dark:bg-surface-800 hover:bg-surface-200 dark:hover:bg-surface-700 transition-colors text-left" on:click={goto('results/mock')}>
				<div class="space-y-3">
					<div class="flex items-center gap-3">
						<div class="text-3xl">📊</div>
						<h3 class="text-xl font-semibold text-primary-500">View Results</h3>
					</div>
					<p class="text-surface-600 dark:text-surface-300">Access completed genome analysis results and reports</p>
					<span class="badge variant-outline-primary">Coming Soon</span>
				</div>
			</button>

			<button class="card p-6 bg-surface-100 dark:bg-surface-800 hover:bg-surface-200 dark:hover:bg-surface-700 transition-colors text-left" on:click={goto('results/historical')}>
				<div class="space-y-3">
					<div class="flex items-center gap-3">
						<div class="text-3xl">📜</div>
						<h3 class="text-xl font-semibold text-secondary-500">Analysis History</h3>
					</div>
					<p class="text-surface-600 dark:text-surface-300">Review previous genome submissions and analyses</p>
					<span class="badge variant-outline-secondary">Coming Soon</span>
				</div>
			</button>
		</div>
	</section>

	<!-- Features Section -->
	<section>
		<h2 class="text-3xl font-bold text-center mb-8">Capabilities</h2>
		<div class="grid grid-cols-1 md:grid-cols-3 gap-6">
			<div class="card p-6 bg-surface-100 dark:bg-surface-800">
				<div class="text-center space-y-4">
					<div class="text-4xl">🧬</div>
					<h3 class="text-xl font-semibold text-primary-500">Sequence Analysis</h3>
					<p class="text-surface-600 dark:text-surface-300">Comprehensive genome sequencing and annotation</p>
				</div>
			</div>

			<div class="card p-6 bg-surface-100 dark:bg-surface-800">
				<div class="text-center space-y-4">
					<div class="text-4xl">⚡</div>
					<h3 class="text-xl font-semibold text-secondary-500">Rapid Processing</h3>
					<p class="text-surface-600 dark:text-surface-300">Fast automated analysis with intelligent algorithms</p>
				</div>
			</div>

			<div class="card p-6 bg-surface-100 dark:bg-surface-800">
				<div class="text-center space-y-4">
					<div class="text-4xl">📈</div>
					<h3 class="text-xl font-semibold text-tertiary-500">Detailed Insights</h3>
					<p class="text-surface-600 dark:text-surface-300">Rich visualization and comprehensive reporting</p>
				</div>
			</div>
		</div>
	</section>

	<!-- Programs & Tools Section -->
	<section>
		<h2 class="text-3xl font-bold text-center mb-8">Analysis Programs & Tools</h2>
		<p class="text-center text-surface-600 dark:text-surface-300 mb-8">MARGIE uses industry-leading bioinformatics tools for comprehensive genome analysis</p>

		<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 max-w-5xl mx-auto">
			<a href="https://github.com/ncbi/genotator" target="_blank" rel="noopener noreferrer" class="card p-5 bg-surface-100 dark:bg-surface-800 hover:bg-surface-200 dark:hover:bg-surface-700 transition-colors">
				<div class="flex items-start justify-between mb-2">
					<h3 class="text-lg font-semibold text-primary-500">genotator</h3>
					<span class="text-xs text-surface-500">v2.1.0</span>
				</div>
				<p class="text-sm text-surface-600 dark:text-surface-300">Genome annotation and feature prediction tool</p>
			</a>

			<a href="https://github.com/ablab/spades" target="_blank" rel="noopener noreferrer" class="card p-5 bg-surface-100 dark:bg-surface-800 hover:bg-surface-200 dark:hover:bg-surface-700 transition-colors">
				<div class="flex items-start justify-between mb-2">
					<h3 class="text-lg font-semibold text-secondary-500">metaSPAdes</h3>
					<span class="text-xs text-surface-500">v3.15</span>
				</div>
				<p class="text-sm text-surface-600 dark:text-surface-300">Metagenomic assembly toolkit for complex samples</p>
			</a>

			<a href="https://github.com/hyattpd/Prodigal" target="_blank" rel="noopener noreferrer" class="card p-5 bg-surface-100 dark:bg-surface-800 hover:bg-surface-200 dark:hover:bg-surface-700 transition-colors">
				<div class="flex items-start justify-between mb-2">
					<h3 class="text-lg font-semibold text-tertiary-500">Prodigal</h3>
					<span class="text-xs text-surface-500">v2.6.3</span>
				</div>
				<p class="text-sm text-surface-600 dark:text-surface-300">Prokaryotic gene recognition and translation</p>
			</a>

			<a href="https://rast.nmpdr.org/" target="_blank" rel="noopener noreferrer" class="card p-5 bg-surface-100 dark:bg-surface-800 hover:bg-surface-200 dark:hover:bg-surface-700 transition-colors">
				<div class="flex items-start justify-between mb-2">
					<h3 class="text-lg font-semibold text-warning-500">RAST</h3>
					<span class="text-xs text-surface-500">v2.0</span>
				</div>
				<p class="text-sm text-surface-600 dark:text-surface-300">Rapid Annotation using Subsystem Technology</p>
			</a>

			<a href="http://tigrfams.jcvi.org/cgi-bin/index.cgi" target="_blank" rel="noopener noreferrer" class="card p-5 bg-surface-100 dark:bg-surface-800 hover:bg-surface-200 dark:hover:bg-surface-700 transition-colors">
				<div class="flex items-start justify-between mb-2">
					<h3 class="text-lg font-semibold text-primary-500">TIGRFAMs</h3>
					<span class="text-xs text-surface-500">v15.0</span>
				</div>
				<p class="text-sm text-surface-600 dark:text-surface-300">Protein family classification and annotation</p>
			</a>
		</div>
	</section>

	<!-- Recent Analyses Section -->
	<section>
		<h2 class="text-3xl font-bold text-center mb-8">Recent Analyses</h2>
		<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
			<div class="card bg-surface-100 dark:bg-surface-800">
				<div class="p-6 space-y-4">
					<div class="flex items-center justify-between">
						<span class="badge variant-filled-success">Completed</span>
						<span class="text-xs text-surface-500">2 hours ago</span>
					</div>
					<h3 class="text-xl font-semibold">E. coli K-12 MG1655</h3>
					<p class="text-surface-600 dark:text-surface-300">Complete genome sequence annotation and analysis</p>
					<div class="flex gap-2">
						<button class="btn variant-ghost-primary flex-1">View Report</button>
						<button class="btn variant-ghost-secondary flex-1">Download</button>
					</div>
				</div>
			</div>

			<div class="card bg-surface-100 dark:bg-surface-800">
				<div class="p-6 space-y-4">
					<div class="flex items-center justify-between">
						<span class="badge variant-filled-warning">Processing</span>
						<span class="text-xs text-surface-500">45 min remaining</span>
					</div>
					<h3 class="text-xl font-semibold">B. longhum</h3>
					<p class="text-surface-600 dark:text-surface-300">Variant calling and functional annotation in progress</p>
					<div class="flex gap-2">
						<button class="btn variant-ghost-secondary flex-1" disabled>View Report</button>
						<button class="btn variant-ghost-secondary flex-1" disabled>Download</button>
					</div>
				</div>
			</div>

			<div class="card bg-surface-100 dark:bg-surface-800">
				<div class="p-6 space-y-4">
					<div class="flex items-center justify-between">
						<span class="badge variant-filled-success">Completed</span>
						<span class="text-xs text-surface-500">1 day ago</span>
					</div>
					<h3 class="text-xl font-semibold">Ruminococcaceae UCG13</h3>
					<p class="text-surface-600 dark:text-surface-300">Phylogenetic analysis and mutation detection</p>
					<div class="flex gap-2">
						<button class="btn variant-ghost-primary flex-1">View Report</button>
						<button class="btn variant-ghost-secondary flex-1">Download</button>
					</div>
				</div>
			</div>
		</div>
	</section>

	<!-- Footer -->
	<footer class="text-center py-8 border-t border-surface-300 dark:border-surface-700">
		<p class="text-surface-600 dark:text-surface-300">© 2025 MARGIE - Mostly Automated & Rapid Genome Intelligence Engine</p>
	</footer>
</div>
