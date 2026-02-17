<script context="module">
	export const router = true;
</script>

<script>
	import { page } from '$app/stores';
	import { setClient } from '@urql/svelte';

	import { urqlClient } from '$lib/stores/urqlClient';
	import Page from '$lib/components/Page.svelte';

	if ($urqlClient) {
		setClient($urqlClient);
	}
	let item = $page.params.item;
	//console.log({ item });
</script>

<Page MenuItem={false} CustomId={item} backPath={`/queries/${$page.params.queryName}`}>
	<div class="p-2">
		{item}
	</div>
</Page>
