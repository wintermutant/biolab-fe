<div class="container mx-auto p-8 max-w-4xl space-y-10">
	<header class="text-center space-y-3">
		<h1 class="text-4xl font-bold text-primary-500">Roadmap</h1>
		<p class="text-lg text-surface-600 dark:text-surface-300">Why this platform exists and where it's going</p>
	</header>

	<!-- The Problem -->
	<section class="space-y-4">
		<h2 class="text-2xl font-bold border-b border-surface-300 dark:border-surface-600 pb-2">The Problem</h2>
		<p class="text-surface-600 dark:text-surface-300 leading-relaxed">
			Bioinformatics is full of powerful programs that are painful to actually use. Every tool has its own
			environment, its own dependencies, its own database requirements, and its own quirks. Getting a single
			program running on a cluster can take hours of debugging. Getting ten programs to run together in a
			reproducible pipeline? That's a week of work before you analyze a single genome.
		</p>
		<p class="text-surface-600 dark:text-surface-300 leading-relaxed">
			And then there's the redundancy problem. You annotate 500 genomes with Prodigal, then you annotate 500
			more &mdash; but 200 of them overlap with your first batch. Without careful bookkeeping, you're burning
			compute hours re-running analyses you already have results for.
		</p>
	</section>

	<!-- The Goal -->
	<section class="space-y-4">
		<h2 class="text-2xl font-bold border-b border-surface-300 dark:border-surface-600 pb-2">The Goal</h2>
		<p class="text-surface-600 dark:text-surface-300 leading-relaxed">
			The goal is simple: you should be able to say <em>"run 1,000 genomes through this pipeline"</em> and
			walk away. The platform handles everything else &mdash; container management, database downloads, SLURM
			job scheduling, memory and CPU allocation, output caching, and result storage.
		</p>
		<div class="grid grid-cols-1 md:grid-cols-2 gap-4 mt-6">
			<div class="card p-5 bg-surface-100 dark:bg-surface-800">
				<h3 class="font-semibold text-primary-500 mb-2">Containerize Once, Run Forever</h3>
				<p class="text-sm text-surface-600 dark:text-surface-300">
					Every program is wrapped in an Apptainer container. Old programs that haven't been
					maintained in years? Containerize them and they run perfectly on any cluster, every time.
					Containers are cached in <code class="text-xs bg-surface-300 dark:bg-surface-600 px-1 rounded">~/.cache</code>
					and pulled automatically.
				</p>
			</div>
			<div class="card p-5 bg-surface-100 dark:bg-surface-800">
				<h3 class="font-semibold text-primary-500 mb-2">Compute Once, Query Forever</h3>
				<p class="text-sm text-surface-600 dark:text-surface-300">
					Every annotation result is stored in a portable database. If a genome has already been
					annotated by a program, the platform queries existing results instead of re-computing.
					No wasted cycles, no redundant data.
				</p>
			</div>
			<div class="card p-5 bg-surface-100 dark:bg-surface-800">
				<h3 class="font-semibold text-primary-500 mb-2">Configure Once, Scale Forever</h3>
				<p class="text-sm text-surface-600 dark:text-surface-300">
					Define a pipeline as a named configuration &mdash; which programs to run, in what order. Then
					throw any number of genomes at it. SLURM parameters like memory, CPUs, partitions, and
					timeouts are handled automatically so you don't have to think about execution details.
				</p>
			</div>
			<div class="card p-5 bg-surface-100 dark:bg-surface-800">
				<h3 class="font-semibold text-primary-500 mb-2">Portable by Design</h3>
				<p class="text-sm text-surface-600 dark:text-surface-300">
					The server only needs a <code class="text-xs bg-surface-300 dark:bg-surface-600 px-1 rounded">pip install</code>,
					a cache directory for containers, and a location for reference databases. The annotation
					database is portable &mdash; move it between clusters, share it with collaborators, or use it
					locally for downstream analysis.
				</p>
			</div>
		</div>
	</section>

	<!-- How It Works -->
	<section class="space-y-4">
		<h2 class="text-2xl font-bold border-b border-surface-300 dark:border-surface-600 pb-2">How It Works</h2>
		<div class="space-y-3">
			<div class="flex gap-4 items-start">
				<span class="text-2xl font-bold text-primary-500 shrink-0">1.</span>
				<div>
					<h3 class="font-semibold">Add a program</h3>
					<p class="text-sm text-surface-600 dark:text-surface-300">
						Containerize any bioinformatics tool with Apptainer. Define its inputs, outputs, and
						database requirements. Publish the container to a registry.
					</p>
				</div>
			</div>
			<div class="flex gap-4 items-start">
				<span class="text-2xl font-bold text-primary-500 shrink-0">2.</span>
				<div>
					<h3 class="font-semibold">Build a pipeline</h3>
					<p class="text-sm text-surface-600 dark:text-surface-300">
						Chain programs together in a Snakemake workflow. The platform auto-generates SLURM
						resource allocations and manages dependencies between steps.
					</p>
				</div>
			</div>
			<div class="flex gap-4 items-start">
				<span class="text-2xl font-bold text-primary-500 shrink-0">3.</span>
				<div>
					<h3 class="font-semibold">Run at scale</h3>
					<p class="text-sm text-surface-600 dark:text-surface-300">
						Submit genomes to the pipeline. The platform checks the database for existing results,
						skips what's already computed, and runs everything else at full throttle on the
						supercomputer.
					</p>
				</div>
			</div>
			<div class="flex gap-4 items-start">
				<span class="text-2xl font-bold text-primary-500 shrink-0">4.</span>
				<div>
					<h3 class="font-semibold">Query and analyze</h3>
					<p class="text-sm text-surface-600 dark:text-surface-300">
						All results live in a master database. Pull data across hundreds or thousands of genomes
						for meta-studies, comparative analyses, or quick lookups &mdash; without ever touching
						the cluster again.
					</p>
				</div>
			</div>
		</div>
	</section>

	<!-- Development Roadmap -->
	<section class="space-y-6">
		<h2 class="text-2xl font-bold border-b border-surface-300 dark:border-surface-600 pb-2">Development Roadmap</h2>

		<!-- Done -->
		<div class="space-y-3">
			<h3 class="text-lg font-semibold text-green-500">Completed</h3>
			<div class="flex items-center gap-3">
				<span class="badge variant-filled-success text-xs">Done</span>
				<span class="text-surface-600 dark:text-surface-300">Containerized program execution via Apptainer on HPC</span>
			</div>
			<div class="flex items-center gap-3">
				<span class="badge variant-filled-success text-xs">Done</span>
				<span class="text-surface-600 dark:text-surface-300">Snakemake workflow generation with SLURM executor</span>
			</div>
			<div class="flex items-center gap-3">
				<span class="badge variant-filled-success text-xs">Done</span>
				<span class="text-surface-600 dark:text-surface-300">Output caching to avoid redundant computation</span>
			</div>
			<div class="flex items-center gap-3">
				<span class="badge variant-filled-success text-xs">Done</span>
				<span class="text-surface-600 dark:text-surface-300">Real-time job monitoring via web dashboard</span>
			</div>
		</div>

		<!-- In Progress -->
		<div class="space-y-3">
			<h3 class="text-lg font-semibold text-warning-500">In Progress</h3>
			<div class="flex items-center gap-3">
				<span class="badge variant-filled-warning text-xs">In Progress</span>
				<span class="text-surface-600 dark:text-surface-300">Portable annotation database with query-before-compute logic</span>
			</div>
			<div class="flex items-center gap-3">
				<span class="badge variant-filled-warning text-xs">In Progress</span>
				<span class="text-surface-600 dark:text-surface-300">Automatic SLURM resource tuning (memory, CPUs, timeouts)</span>
			</div>
			<div class="flex items-center gap-3">
				<span class="badge variant-filled-warning text-xs">In Progress</span>
				<span class="text-surface-600 dark:text-surface-300">SQLite3 database page &mdash; browse and query stored annotation results from the frontend</span>
			</div>
		</div>

		<!-- Planned -->
		<div class="space-y-3">
			<h3 class="text-lg font-semibold text-surface-500">Planned</h3>
			<div class="flex items-center gap-3">
				<span class="badge variant-soft text-xs">Planned</span>
				<span class="text-surface-600 dark:text-surface-300">User config database &mdash; store SSH hostname, username, key paths, and preferences per user instead of hardcoded values</span>
			</div>
			<div class="flex items-center gap-3">
				<span class="badge variant-soft text-xs">Planned</span>
				<span class="text-surface-600 dark:text-surface-300">Remove hardcoded SSH config &mdash; pull connection details from user config database</span>
			</div>
			<div class="flex items-center gap-3">
				<span class="badge variant-soft text-xs">Planned</span>
				<span class="text-surface-600 dark:text-surface-300">Program registry &mdash; define each tool's name, command template, inputs/outputs, and Snakemake rule mapping in a database</span>
			</div>
			<div class="flex items-center gap-3">
				<span class="badge variant-soft text-xs">Planned</span>
				<span class="text-surface-600 dark:text-surface-300">Program selection UI &mdash; checkboxes on the analyze page to pick which programs to run (requires program registry)</span>
			</div>
			<div class="flex items-center gap-3">
				<span class="badge variant-soft text-xs">Planned</span>
				<span class="text-surface-600 dark:text-surface-300">Batch submission &mdash; run N genomes through a named pipeline config</span>
			</div>
			<div class="flex items-center gap-3">
				<span class="badge variant-soft text-xs">Planned</span>
				<span class="text-surface-600 dark:text-surface-300">Self-service program onboarding &mdash; containerize and register new tools from the UI</span>
			</div>
			<div class="flex items-center gap-3">
				<span class="badge variant-soft text-xs">Planned</span>
				<span class="text-surface-600 dark:text-surface-300">Cross-genome meta-analysis queries from the master database</span>
			</div>
		</div>
	</section>
</div>
