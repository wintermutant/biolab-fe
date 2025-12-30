<script lang="ts">
	import Navibar from "$lib/Navibar.svelte";
	import { Upload } from 'lucide-svelte';

	// SSH Credentials
	let sshUsername = '';
	let sshHost = '';
	let sshPublicKey = '';

	// Resources Configuration
	let maxCpuCores = 8;
	let maxMemoryGB = 16;
	let maxStorageGB = 100;

	// Logging Configuration
	let logLevel = 'info';
	let logRetentionDays = 30;
	let enableDebugMode = false;

	// Program-Specific Configuration
	// genotator
	let genotatorEnabled = true;
	let genotatorVersion = '2.1.0';
	let genotatorThreads = 4;
	let genotatorMinScore = 0.75;

	// metaspades
	let metaspadesEnabled = true;
	let metaspadesKmerSize = 21;
	let metaspadesMemoryLimit = 32;
	let metaspadesCareful = true;

	// prodigal
	let prodigalEnabled = true;
	let prodigalMode = 'meta';
	let prodigalClosedEnds = false;
	let prodigalTransTable = 11;

	// RAST
	let rastEnabled = true;
	let rastDomain = 'Bacteria';
	let rastGeneticCode = 11;
	let rastFixErrors = true;

	// tigrfam
	let tigrfamEnabled = true;
	let tigrfamCutoff = '1e-5';
	let tigrfamCoverage = 0.8;
	let tigrfamHmmerThreads = 8;

	// File upload
	let isDragging = false;
	let configFile: File | null = null;

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
		const files = e.dataTransfer?.files;
		if (files && files.length > 0) {
			configFile = files[0];
			parseConfigFile(configFile);
		}
	}

	function handleFileSelect(e: Event) {
		const target = e.target as HTMLInputElement;
		if (target.files && target.files.length > 0) {
			configFile = target.files[0];
			parseConfigFile(configFile);
		}
	}

	async function parseConfigFile(file: File) {
		// TODO: Parse YAML file and populate fields
		const text = await file.text();
		console.log('Config file content:', text);
		alert(`Config file "${file.name}" uploaded! In production, this would parse the YAML and populate all fields.`);

		// Mock population - in real implementation, parse YAML here
		// Example: use js-yaml library to parse and populate fields
	}

	function saveProfile() {
		// TODO: API call to save profile settings
		console.log('Saving profile...');
		alert('Profile settings would be saved here!');
	}
</script>

<Navibar/>

<div class="container mx-auto p-8 space-y-8 max-w-4xl">
	<!-- Page Header -->
	<section class="text-center py-8">
		<h1 class="text-4xl font-bold text-primary-500 mb-4">Profile Settings</h1>
		<p class="text-lg text-surface-600 dark:text-surface-300">Manage your SSH credentials and configuration settings</p>
	</section>

	<!-- SSH Credentials Section -->
	<section class="card p-6 bg-surface-100 dark:bg-surface-800">
		<h2 class="text-2xl font-bold mb-6 text-primary-500">SSH Credentials</h2>

		<div class="space-y-4">
			<!-- SSH Username -->
			<div>
				<label for="sshUsername" class="block text-sm font-semibold mb-2">SSH Username</label>
				<input
					id="sshUsername"
					type="text"
					bind:value={sshUsername}
					placeholder="Enter your SSH username"
					class="input w-full px-4 py-2 rounded-lg bg-surface-200 dark:bg-surface-700 border border-surface-300 dark:border-surface-600"
				/>
			</div>

			<!-- SSH Host -->
			<div>
				<label for="sshHost" class="block text-sm font-semibold mb-2">SSH Host</label>
				<input
					id="sshHost"
					type="text"
					bind:value={sshHost}
					placeholder="e.g., example.server.com"
					class="input w-full px-4 py-2 rounded-lg bg-surface-200 dark:bg-surface-700 border border-surface-300 dark:border-surface-600"
				/>
			</div>

			<!-- SSH Public Key -->
			<div>
				<label for="sshPublicKey" class="block text-sm font-semibold mb-2">SSH Public Key</label>
				<textarea
					id="sshPublicKey"
					bind:value={sshPublicKey}
					placeholder="Paste your SSH public key here (e.g., ssh-rsa AAAA...)"
					rows="4"
					class="input w-full px-4 py-2 rounded-lg bg-surface-200 dark:bg-surface-700 border border-surface-300 dark:border-surface-600 font-mono text-sm"
				></textarea>
				<p class="text-xs text-surface-500 mt-2">Your public key will be used for secure authentication</p>
			</div>
		</div>
	</section>

	<!-- Config File Upload Section -->
	<section class="card p-6 bg-surface-100 dark:bg-surface-800">
		<h2 class="text-2xl font-bold mb-6 text-primary-500">Upload Configuration File</h2>

		<!-- Drag & Drop Zone -->
		<div
			class="border-2 border-dashed rounded-lg p-8 text-center transition-colors {isDragging ? 'border-primary-500 bg-primary-100 dark:bg-primary-900/20' : 'border-surface-400 dark:border-surface-600'}"
			on:dragover={handleDragOver}
			on:dragleave={handleDragLeave}
			on:drop={handleDrop}
		>
			<Upload class="mx-auto mb-4 size-12 text-surface-500" />
			<p class="text-lg mb-2 font-semibold">Drag & Drop config.yaml Here</p>
			<p class="text-surface-600 dark:text-surface-400 mb-4">or</p>
			<label class="btn variant-filled-secondary cursor-pointer">
				<input
					type="file"
					accept=".yaml,.yml"
					class="hidden"
					on:change={handleFileSelect}
				/>
				Browse Files
			</label>
			{#if configFile}
				<div class="mt-4 p-3 bg-success-100 dark:bg-success-900/20 rounded-lg">
					<p class="text-sm text-success-700 dark:text-success-300">
						Uploaded: <strong>{configFile.name}</strong>
					</p>
				</div>
			{/if}
		</div>
	</section>

	<!-- Configuration Settings Section -->
	<section class="card p-6 bg-surface-100 dark:bg-surface-800">
		<h2 class="text-2xl font-bold mb-6 text-secondary-500">Configuration Settings</h2>

		<!-- Resources Subsection -->
		<div class="mb-8">
			<h3 class="text-xl font-semibold mb-4 text-tertiary-500 border-b border-surface-300 dark:border-surface-600 pb-2">Resources</h3>

			<div class="space-y-4">
				<!-- Max CPU Cores -->
				<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
					<label for="maxCpuCores" class="text-sm font-semibold">Maximum CPU Cores</label>
					<input
						id="maxCpuCores"
						type="number"
						bind:value={maxCpuCores}
						min="1"
						max="64"
						class="input px-4 py-2 rounded-lg bg-surface-200 dark:bg-surface-700 border border-surface-300 dark:border-surface-600"
					/>
				</div>

				<!-- Max Memory -->
				<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
					<label for="maxMemoryGB" class="text-sm font-semibold">Maximum Memory (GB)</label>
					<input
						id="maxMemoryGB"
						type="number"
						bind:value={maxMemoryGB}
						min="1"
						max="256"
						class="input px-4 py-2 rounded-lg bg-surface-200 dark:bg-surface-700 border border-surface-300 dark:border-surface-600"
					/>
				</div>

				<!-- Max Storage -->
				<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
					<label for="maxStorageGB" class="text-sm font-semibold">Maximum Storage (GB)</label>
					<input
						id="maxStorageGB"
						type="number"
						bind:value={maxStorageGB}
						min="1"
						max="1000"
						class="input px-4 py-2 rounded-lg bg-surface-200 dark:bg-surface-700 border border-surface-300 dark:border-surface-600"
					/>
				</div>
			</div>
		</div>

		<!-- Logging Subsection -->
		<div class="mb-8">
			<h3 class="text-xl font-semibold mb-4 text-tertiary-500 border-b border-surface-300 dark:border-surface-600 pb-2">Logging</h3>

			<div class="space-y-4">
				<!-- Log Level -->
				<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
					<label for="logLevel" class="text-sm font-semibold">Log Level</label>
					<select
						id="logLevel"
						bind:value={logLevel}
						class="input px-4 py-2 rounded-lg bg-surface-200 dark:bg-surface-700 border border-surface-300 dark:border-surface-600"
					>
						<option value="debug">Debug</option>
						<option value="info">Info</option>
						<option value="warning">Warning</option>
						<option value="error">Error</option>
					</select>
				</div>

				<!-- Log Retention -->
				<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
					<div>
						<label for="logRetentionDays" class="text-sm font-semibold">Log Retention (Days)</label>
						<p class="text-xs text-surface-500">How long to keep log files</p>
					</div>
					<input
						id="logRetentionDays"
						type="number"
						bind:value={logRetentionDays}
						min="1"
						max="365"
						class="input px-4 py-2 rounded-lg bg-surface-200 dark:bg-surface-700 border border-surface-300 dark:border-surface-600"
					/>
				</div>

				<!-- Debug Mode -->
				<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
					<div>
						<label for="enableDebugMode" class="text-sm font-semibold">Enable Debug Mode</label>
						<p class="text-xs text-surface-500">Verbose logging for troubleshooting</p>
					</div>
					<label class="flex items-center gap-2">
						<input
							id="enableDebugMode"
							type="checkbox"
							bind:checked={enableDebugMode}
							class="checkbox"
						/>
						<span class="text-sm">{enableDebugMode ? 'Enabled' : 'Disabled'}</span>
					</label>
				</div>
			</div>
		</div>

		<!-- Program Settings Subsection with Accordion -->
		<div>
			<h3 class="text-xl font-semibold mb-4 text-tertiary-500 border-b border-surface-300 dark:border-surface-600 pb-2">Program Settings</h3>

			<div class="space-y-3">
				<!-- genotator -->
				<details class="group bg-surface-200 dark:bg-surface-700 rounded-lg">
					<summary class="cursor-pointer p-4 font-semibold flex justify-between items-center hover:bg-surface-300 dark:hover:bg-surface-600 rounded-lg transition-colors">
						<span>genotator</span>
						<span class="transform transition-transform group-open:rotate-180">▼</span>
					</summary>
					<div class="p-4 pt-0 space-y-3">
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Enabled</label>
							<input type="checkbox" bind:checked={genotatorEnabled} class="checkbox" />
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Version</label>
							<input type="text" bind:value={genotatorVersion} class="input px-3 py-2 rounded bg-surface-300 dark:bg-surface-600" />
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Threads</label>
							<input type="number" bind:value={genotatorThreads} min="1" max="32" class="input px-3 py-2 rounded bg-surface-300 dark:bg-surface-600" />
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Minimum Score</label>
							<input type="number" bind:value={genotatorMinScore} min="0" max="1" step="0.01" class="input px-3 py-2 rounded bg-surface-300 dark:bg-surface-600" />
						</div>
					</div>
				</details>

				<!-- metaspades -->
				<details class="group bg-surface-200 dark:bg-surface-700 rounded-lg">
					<summary class="cursor-pointer p-4 font-semibold flex justify-between items-center hover:bg-surface-300 dark:hover:bg-surface-600 rounded-lg transition-colors">
						<span>metaspades</span>
						<span class="transform transition-transform group-open:rotate-180">▼</span>
					</summary>
					<div class="p-4 pt-0 space-y-3">
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Enabled</label>
							<input type="checkbox" bind:checked={metaspadesEnabled} class="checkbox" />
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">K-mer Size</label>
							<input type="number" bind:value={metaspadesKmerSize} min="1" max="127" class="input px-3 py-2 rounded bg-surface-300 dark:bg-surface-600" />
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Memory Limit (GB)</label>
							<input type="number" bind:value={metaspadesMemoryLimit} min="1" max="256" class="input px-3 py-2 rounded bg-surface-300 dark:bg-surface-600" />
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Careful Mode</label>
							<input type="checkbox" bind:checked={metaspadesCareful} class="checkbox" />
						</div>
					</div>
				</details>

				<!-- prodigal -->
				<details class="group bg-surface-200 dark:bg-surface-700 rounded-lg">
					<summary class="cursor-pointer p-4 font-semibold flex justify-between items-center hover:bg-surface-300 dark:hover:bg-surface-600 rounded-lg transition-colors">
						<span>prodigal</span>
						<span class="transform transition-transform group-open:rotate-180">▼</span>
					</summary>
					<div class="p-4 pt-0 space-y-3">
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Enabled</label>
							<input type="checkbox" bind:checked={prodigalEnabled} class="checkbox" />
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Mode</label>
							<select bind:value={prodigalMode} class="input px-3 py-2 rounded bg-surface-300 dark:bg-surface-600">
								<option value="meta">Meta</option>
								<option value="single">Single</option>
							</select>
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Closed Ends</label>
							<input type="checkbox" bind:checked={prodigalClosedEnds} class="checkbox" />
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Translation Table</label>
							<input type="number" bind:value={prodigalTransTable} min="1" max="25" class="input px-3 py-2 rounded bg-surface-300 dark:bg-surface-600" />
						</div>
					</div>
				</details>

				<!-- RAST -->
				<details class="group bg-surface-200 dark:bg-surface-700 rounded-lg">
					<summary class="cursor-pointer p-4 font-semibold flex justify-between items-center hover:bg-surface-300 dark:hover:bg-surface-600 rounded-lg transition-colors">
						<span>RAST</span>
						<span class="transform transition-transform group-open:rotate-180">▼</span>
					</summary>
					<div class="p-4 pt-0 space-y-3">
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Enabled</label>
							<input type="checkbox" bind:checked={rastEnabled} class="checkbox" />
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Domain</label>
							<select bind:value={rastDomain} class="input px-3 py-2 rounded bg-surface-300 dark:bg-surface-600">
								<option value="Bacteria">Bacteria</option>
								<option value="Archaea">Archaea</option>
								<option value="Virus">Virus</option>
							</select>
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Genetic Code</label>
							<input type="number" bind:value={rastGeneticCode} min="1" max="25" class="input px-3 py-2 rounded bg-surface-300 dark:bg-surface-600" />
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Fix Errors</label>
							<input type="checkbox" bind:checked={rastFixErrors} class="checkbox" />
						</div>
					</div>
				</details>

				<!-- tigrfam -->
				<details class="group bg-surface-200 dark:bg-surface-700 rounded-lg">
					<summary class="cursor-pointer p-4 font-semibold flex justify-between items-center hover:bg-surface-300 dark:hover:bg-surface-600 rounded-lg transition-colors">
						<span>tigrfam</span>
						<span class="transform transition-transform group-open:rotate-180">▼</span>
					</summary>
					<div class="p-4 pt-0 space-y-3">
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Enabled</label>
							<input type="checkbox" bind:checked={tigrfamEnabled} class="checkbox" />
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">E-value Cutoff</label>
							<input type="text" bind:value={tigrfamCutoff} placeholder="1e-5" class="input px-3 py-2 rounded bg-surface-300 dark:bg-surface-600 font-mono" />
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">Coverage Threshold</label>
							<input type="number" bind:value={tigrfamCoverage} min="0" max="1" step="0.01" class="input px-3 py-2 rounded bg-surface-300 dark:bg-surface-600" />
						</div>
						<div class="grid grid-cols-1 md:grid-cols-2 gap-4 items-center">
							<label class="text-sm font-semibold">HMMER Threads</label>
							<input type="number" bind:value={tigrfamHmmerThreads} min="1" max="64" class="input px-3 py-2 rounded bg-surface-300 dark:bg-surface-600" />
						</div>
					</div>
				</details>
			</div>
		</div>
	</section>

	<!-- Save Button -->
	<div class="flex justify-center gap-4">
		<button
			class="btn variant-filled-primary btn-lg px-8"
			on:click={saveProfile}
		>
			Save Changes
		</button>
		<button
			class="btn variant-outline-secondary btn-lg px-8"
		>
			Cancel
		</button>
	</div>

	<!-- Info Message -->
	<div class="card p-4 bg-warning-500/10 border border-warning-500/30">
		<p class="text-sm text-warning-700 dark:text-warning-300 text-center">
			<strong>Note:</strong> All settings are placeholders and will be connected to the backend API in a future update.
		</p>
	</div>
</div>
