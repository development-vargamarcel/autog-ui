<script>
	import Input from '$lib/Input.svelte';
	import Map from '$lib/Map.svelte';
	import Toggle from '$lib/Toggle.svelte';

	export let dd_displayType;
	export let dd_displayStructure;
	export let rawValue;

	let componentToRender = Input;
	if (['text', 'number', 'date', 'datetime-local'].includes(dd_displayType)) {
		componentToRender = Input;
	}
	if (['geo'].includes(dd_displayType)) {
		componentToRender = Map;
	}
	if (['boolean'].includes(dd_displayType)) {
		componentToRender = Toggle;
	}
</script>

<svelte:component
	this={componentToRender}
	{dd_displayType}
	{dd_displayStructure}
	{rawValue}
	on:changed
/>
