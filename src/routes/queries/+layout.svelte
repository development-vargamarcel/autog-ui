<script>
	import { getStores, navigating, page, updated } from '$app/stores';

	import { onDestroy } from 'svelte';
	//console.log('queries: ', queries);

	let origin = $page.url.origin;
	let pathname;

	let pageUnsubscribe = page.subscribe((value) => {
		pathname = value.url.pathname;
		//console.log('value===', value);
	});
	onDestroy(() => {
		pageUnsubscribe();
	});
</script>

<div class="w-full pt-2">
	{#key $page.params.queryName}
		<slot />
	{/key}
</div>
