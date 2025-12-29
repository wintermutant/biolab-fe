<script lang="ts">
  import { Calendar, CircleUser, Menu, Search } from 'lucide-svelte';
  import { AppBar } from '@skeletonlabs/skeleton-svelte'
</script>

<AppBar>
  	<AppBar.Toolbar class="grid-cols-[auto_1fr_auto]">
		<AppBar.Lead>
			<button type="button" class="btn-icon btn-icon-lg hover:preset-tonal"><Menu /></button>
		</AppBar.Lead>
		<AppBar.Headline>
			<p class="text-2xl font-bold">MARGIE</p>
		</AppBar.Headline>
		<AppBar.Trail>
			<button type="button" class="btn-icon hover:preset-tonal"><Search class="size-6" /></button>
			<button type="button" class="btn-icon hover:preset-tonal"><Calendar class="size-6" /></button>
			<button type="button" class="btn-icon hover:preset-tonal"><CircleUser class="size-6" /></button>
		</AppBar.Trail>
	</AppBar.Toolbar>
</AppBar>