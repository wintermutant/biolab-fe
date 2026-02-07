<script lang="ts">
	import { page } from '$app/stores';
	import { onMount, onDestroy } from 'svelte';

	function getApiUrl() {
		if (typeof window !== 'undefined' && window.location.hostname === 'localhost') {
			return 'http://localhost:8000';
		}
		return '';
	}

	interface SubJob {
		id: string;
		name: string;
		status: 'pending' | 'running' | 'completed' | 'failed';
		start_time?: string;
		end_time?: string;
	}

	interface JobStatus {
		job_id: string;
		status: 'pending' | 'running' | 'snakemake' | 'completed' | 'failed';
		phase: string;
		progress?: number;
		start_time?: string;
		end_time?: string;
		genome_path?: string;
		sub_jobs: SubJob[];
		logs?: string;
	}

	let jobId = $derived($page.params.jobid);
	let job = $state<JobStatus | null>(null);
	let error = $state('');
	let loading = $state(true);
	let pollInterval: ReturnType<typeof setInterval> | null = null;
	let showLogs = $state(true);  // Show logs by default

	onMount(() => {
		fetchJobStatus();
		pollInterval = setInterval(fetchJobStatus, 10000);  // Poll every 2 seconds for responsive logs
	});

	onDestroy(() => {
		if (pollInterval) clearInterval(pollInterval);
	});

	async function fetchJobStatus() {
		console.log('Fetching job status...')
		try {
			const apiUrl = getApiUrl();
			console.log('API URL:', apiUrl, 'Job ID:', jobId);
			const res = await fetch(`${apiUrl}/v1/ssh/job_status/${jobId}`);
			if (!res.ok) throw new Error('Failed to fetch job status');
			job = await res.json();
			error = '';
			console.log(`Fetched the ${apiUrl}/v1/ssh/job_status/${jobId}`)

			// Stop polling if job is done
			if (job && (job.status === 'completed' || job.status === 'failed')) {
				if (pollInterval) {
					clearInterval(pollInterval);
					pollInterval = null;
				}
			}
		} catch (e) {
			console.error('Error fetching api endpoint or something')
			error = e instanceof Error ? e.message : 'Failed to fetch job status';
		} finally {
			loading = false;
		}
	}

	function getStatusColor(status: string): string {
		switch (status) {
			case 'completed': return 'bg-green-500';
			case 'running': return 'bg-blue-500 animate-pulse';
			case 'snakemake': return 'bg-purple-500 animate-pulse';
			case 'failed': return 'bg-red-500';
			default: return 'bg-gray-400';
		}
	}

	function getStatusText(status: string): string {
		switch (status) {
			case 'completed': return 'Completed';
			case 'running': return 'Running';
			case 'snakemake': return 'Running Snakemake';
			case 'failed': return 'Failed';
			default: return 'Pending';
		}
	}

	function formatTime(time?: string): string {
		if (!time) return '-';
		return new Date(time).toLocaleString();
	}

	$effect(() => {
		// Refetch when jobId changes (only in browser)
		if (typeof window !== 'undefined' && jobId) {
			loading = true;
			fetchJobStatus();
		}
	});
</script>

<div class="container mx-auto p-8 max-w-5xl space-y-6">
	<div class="flex items-center justify-between">
		<h1 class="text-3xl font-bold text-primary-500">Job Monitor</h1>
		<a href="/analyze" class="btn variant-ghost-surface px-4 py-2">New Analysis</a>
	</div>

	{#if error}
		<div class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded">{error}</div>
	{/if}

	{#if loading && !job}
		<div class="card p-8 bg-surface-100 dark:bg-surface-800 text-center">
			<p class="text-surface-500">Loading job status...</p>
		</div>
	{:else if job}
		<!-- Main Job Status -->
		<section class="card p-6 bg-surface-100 dark:bg-surface-800">
			<div class="flex items-start justify-between mb-4">
				<div>
					<h2 class="text-xl font-semibold mb-1">Job ID</h2>
					<code class="font-mono text-sm bg-surface-200 dark:bg-surface-700 px-2 py-1 rounded">{job.job_id}</code>
				</div>
				<div class="flex items-center gap-2">
					<span class="inline-block w-3 h-3 rounded-full {getStatusColor(job.status)}"></span>
					<span class="font-semibold">{getStatusText(job.status)}</span>
				</div>
			</div>

			{#if job.genome_path}
				<div class="mb-4">
					<span class="text-sm text-surface-500">Genome:</span>
					<span class="font-mono text-sm ml-2">{job.genome_path}</span>
				</div>
			{/if}

			<div class="grid grid-cols-2 md:grid-cols-4 gap-4 text-sm">
				<div>
					<span class="text-surface-500 block">Phase</span>
					<span class="font-semibold">{job.phase || 'Initializing'}</span>
				</div>
				<div>
					<span class="text-surface-500 block">Started</span>
					<span>{formatTime(job.start_time)}</span>
				</div>
				<div>
					<span class="text-surface-500 block">Ended</span>
					<span>{formatTime(job.end_time)}</span>
				</div>
				{#if job.progress !== undefined}
					<div>
						<span class="text-surface-500 block">Progress</span>
						<span>{job.progress}%</span>
					</div>
				{/if}
			</div>

			{#if job.progress !== undefined}
				<div class="mt-4">
					<div class="w-full bg-surface-300 dark:bg-surface-600 rounded-full h-2">
						<div
							class="h-2 rounded-full transition-all duration-500 {job.status === 'failed' ? 'bg-red-500' : 'bg-primary-500'}"
							style="width: {job.progress}%"
						></div>
					</div>
				</div>
			{/if}
		</section>

		<!-- Snakemake Sub-Jobs -->
		{#if job.sub_jobs && job.sub_jobs.length > 0}
			<section class="card p-6 bg-surface-100 dark:bg-surface-800">
				<h2 class="text-xl font-semibold mb-4">Snakemake Jobs</h2>
				<div class="space-y-2">
					{#each job.sub_jobs as subJob}
						<div class="flex items-center justify-between p-3 bg-surface-200 dark:bg-surface-700 rounded-lg">
							<div class="flex items-center gap-3">
								<span class="inline-block w-2.5 h-2.5 rounded-full {getStatusColor(subJob.status)}"></span>
								<span class="font-mono text-sm">{subJob.name}</span>
							</div>
							<div class="flex items-center gap-4 text-sm text-surface-500">
								<span>ID: {subJob.id}</span>
								{#if subJob.start_time}
									<span>Started: {formatTime(subJob.start_time)}</span>
								{/if}
								{#if subJob.end_time}
									<span>Ended: {formatTime(subJob.end_time)}</span>
								{/if}
							</div>
						</div>
					{/each}
				</div>
			</section>
		{:else if job.status === 'snakemake'}
			<section class="card p-6 bg-surface-100 dark:bg-surface-800">
				<h2 class="text-xl font-semibold mb-4">Snakemake Jobs</h2>
				<p class="text-surface-500">Waiting for snakemake jobs to spawn...</p>
			</section>
		{/if}

		<!-- Logs Section -->
		<section class="card p-6 bg-surface-100 dark:bg-surface-800">
			<button
				type="button"
				onclick={() => showLogs = !showLogs}
				class="flex items-center justify-between w-full text-left"
			>
				<h2 class="text-xl font-semibold">Logs</h2>
				<span class="transform transition-transform {showLogs ? 'rotate-180' : ''}">&#9660;</span>
			</button>
			{#if showLogs}
				<div class="mt-4 p-4 bg-surface-900 text-green-400 font-mono text-sm rounded-lg overflow-x-auto max-h-96 overflow-y-auto">
					{#if job.logs}
						{#each job.logs.split('\n').filter(line => line.trim()) as line, i}
							<div class="py-1 {i > 0 ? 'border-t border-surface-700' : ''}">
								<span class="text-surface-500 mr-2 select-none">{i + 1}</span>{line}
							</div>
						{/each}
					{:else}
						<p class="text-surface-500">No logs available yet.</p>
					{/if}
				</div>
			{/if}
		</section>

		<!-- Status Messages -->
		{#if job.status === 'completed'}
			<div class="bg-green-100 border border-green-400 text-green-700 px-4 py-3 rounded">
				Analysis completed successfully. Results are ready.
			</div>
		{:else if job.status === 'failed'}
			<div class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded">
				Analysis failed. Check the logs for details.
			</div>
		{:else}
			<div class="bg-blue-100 border border-blue-400 text-blue-700 px-4 py-3 rounded flex items-center gap-2">
				<svg class="animate-spin h-4 w-4" viewBox="0 0 24 24">
					<circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4" fill="none"></circle>
					<path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
				</svg>
				<span>Job is running. This page will update automatically.</span>
			</div>
		{/if}
	{:else}
		<div class="card p-8 bg-surface-100 dark:bg-surface-800 text-center">
			<p class="text-surface-500">Job not found.</p>
		</div>
	{/if}
</div>
