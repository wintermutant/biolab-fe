<script lang="ts">
	import { goto } from '$app/navigation';

	const API_URL = typeof window !== 'undefined' && window.location.hostname === 'localhost'
		? 'http://localhost:8000'
		: '';

	let username = $state('');
	let password = $state('');
	let clusterHost = $state('');
	let clusterUsername = $state('');
	let privateKey = $state('');
	let loading = $state(false);
	let error = $state('');

	async function handleRegister(e: Event) {
		e.preventDefault();
		loading = true;
		error = '';

		try {
			const res = await fetch(`${API_URL}/v1/auth/register`, {
				method: 'POST',
				headers: { 'Content-Type': 'application/json' },
				body: JSON.stringify({
					username,
					password,
					cluster_host: clusterHost,
					cluster_username: clusterUsername,
					private_key: privateKey,
				}),
			});

			const data = await res.json();

			if (!res.ok) {
				error = data.detail ?? 'Registration failed. Please try again.';
				return;
			}

			// Registration also verifies SSH works — safe to send to login now
			goto('/login?registered=1');
		} catch (e) {
			error = e instanceof Error ? e.message : 'Registration failed. Please try again.';
		} finally {
			loading = false;
		}
	}
</script>

<div class="container mx-auto p-8 max-w-lg">
	<section class="text-center py-8">
		<h1 class="text-4xl font-bold text-primary-500 mb-2">Create Account</h1>
		<p class="text-surface-600 dark:text-surface-300">Bioinformatics Supercomputing Platform</p>
	</section>

	{#if error}
		<div class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded mb-4">{error}</div>
	{/if}

	<div class="card p-8 bg-surface-100 dark:bg-surface-800">
		<form onsubmit={handleRegister} class="space-y-5">

			<div class="border-b border-surface-300 dark:border-surface-600 pb-4">
				<h2 class="text-lg font-semibold mb-3 text-secondary-500">BSP Account</h2>
				<div class="space-y-4">
					<div>
						<label for="username" class="block text-sm font-semibold mb-1">Username</label>
						<input
							id="username"
							type="text"
							bind:value={username}
							required
							disabled={loading}
							class="input w-full px-4 py-2 rounded-lg bg-surface-200 dark:bg-surface-700 border border-surface-300 dark:border-surface-600"
						/>
					</div>
					<div>
						<label for="password" class="block text-sm font-semibold mb-1">Password</label>
						<input
							id="password"
							type="password"
							bind:value={password}
							required
							disabled={loading}
							class="input w-full px-4 py-2 rounded-lg bg-surface-200 dark:bg-surface-700 border border-surface-300 dark:border-surface-600"
						/>
					</div>
				</div>
			</div>

			<div>
				<h2 class="text-lg font-semibold mb-3 text-secondary-500">Cluster Connection</h2>
				<div class="space-y-4">
					<div>
						<label for="clusterHost" class="block text-sm font-semibold mb-1">Cluster Host</label>
						<input
							id="clusterHost"
							type="text"
							bind:value={clusterHost}
							placeholder="e.g. negishi.rcac.purdue.edu"
							required
							disabled={loading}
							class="input w-full px-4 py-2 rounded-lg bg-surface-200 dark:bg-surface-700 border border-surface-300 dark:border-surface-600 font-mono text-sm"
						/>
					</div>
					<div>
						<label for="clusterUsername" class="block text-sm font-semibold mb-1">Cluster Username</label>
						<input
							id="clusterUsername"
							type="text"
							bind:value={clusterUsername}
							placeholder="e.g. ddeemer"
							required
							disabled={loading}
							class="input w-full px-4 py-2 rounded-lg bg-surface-200 dark:bg-surface-700 border border-surface-300 dark:border-surface-600 font-mono text-sm"
						/>
					</div>
					<div>
						<label for="privateKey" class="block text-sm font-semibold mb-1">SSH Private Key</label>
						<textarea
							id="privateKey"
							bind:value={privateKey}
							placeholder="Paste your SSH private key here (-----BEGIN ... PRIVATE KEY-----)"
							rows="6"
							required
							disabled={loading}
							class="input w-full px-4 py-2 rounded-lg bg-surface-200 dark:bg-surface-700 border border-surface-300 dark:border-surface-600 font-mono text-xs"
						></textarea>
						<p class="text-xs text-surface-500 mt-1">
							The key is encrypted before storage and never returned by the API.
							BSP will verify your cluster connection during registration.
						</p>
					</div>
				</div>
			</div>

			<button
				type="submit"
				disabled={loading}
				class="btn variant-filled-primary w-full py-3"
			>
				{loading ? 'Verifying connection & registering...' : 'Create Account'}
			</button>
		</form>
	</div>

	<p class="text-center text-sm text-surface-500 mt-6">
		Already have an account? <a href="/login" class="text-primary-500 hover:underline">Sign in</a>
	</p>
</div>
