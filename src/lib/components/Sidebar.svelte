<script>
	import { clickOutside } from '$lib/actions/clickOutside';

	import TabContainer from '$lib/components/TabContainer.svelte';
	export let forceVisibleSidebar = false;
	export let portalSelector;
	export let links;
	import { fade, fly } from 'svelte/transition';

	$: console.log({ forceVisibleSidebar });
</script>

<!-- on:click_outside={() => {
		if (forceVisibleSidebar) {
			forceVisibleSidebar = false;
		}
	}} -->

<div
	class="w-full h-screen  {forceVisibleSidebar
		? 'visible '
		: ' invisible'} fixed left-0  z-50  md:z-0 md:visible md:static flex  "
	use:clickOutside
	on:click={() => {
		if (forceVisibleSidebar) {
			forceVisibleSidebar = false;
		}
	}}
>
	<div class="invisible md:visible">
		<TabContainer />
	</div>
</div>
{#if forceVisibleSidebar}
	<div
		class=" bg-base-100/50 fixed top-0 z-50 md:hidden h-screen w-screen"
		in:fade={{ duration: 300, opacity: 1 }}
		out:fade={{ duration: 300, opacity: 1 }}
	/>
	<div
		class="md:hidden fixed top-0 z-50"
		in:fly={{ x: -300, duration: 300, opacity: 1 }}
		out:fly={{ x: -350, duration: 300, opacity: 1 }}
	>
		<TabContainer
			on:hideSidebar={() => {
				if (forceVisibleSidebar) {
					forceVisibleSidebar = false;
				}
			}}
		/>
	</div>
{/if}
