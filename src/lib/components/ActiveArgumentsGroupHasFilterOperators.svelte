<script>
	import { flip } from 'svelte/animate';
	import { dndzone, SHADOW_PLACEHOLDER_ITEM_ID } from 'svelte-dnd-action';
	import { createEventDispatcher, getContext } from 'svelte';
	import ActiveArgument from '$lib/components/ActiveArgument.svelte';
	const dispatch = createEventDispatcher();
	export let nodes;
	export let node;
	export let availableOperators;
	export let group;
	export let type;
	export let originalNodes;
	let dragDisabled = true;
	let isDraggingStore = getContext('isDraggingStore');
	const flipDurationMs = 100;
	function handleDndConsider(e) {
		//console.log('considering', e, nodes);
		node.items = e.detail.items;
		dragDisabled = true;
		isDraggingStore.set(true);
	}
	function handleDndFinalize(e) {
		node.items = e.detail.items;
		//console.log(e);
		nodes = { ...nodes };
		handleChanged();
		dispatch('changed');
		dragDisabled = true;
		isDraggingStore.set(false);
	}

	const deleteItem = (e) => {
		node.items = node.items.filter((item) => {
			return item.id !== e.detail.id;
		});
		// nodes[e.detail.id] = undefined;
		//!!! to do: also delete the node from "nodes"
		nodes = { ...nodes };
		handleChanged();
		dispatch('changed');
	};
	//
	let labelEl;
	let shadowEl;
	let shadowHeight = 20;
	let shadowWidth = 20;

	let labelElClone;

	$: if (labelEl) {
		shadowHeight = labelEl.clientHeight;
		shadowWidth = labelEl.clientWidth;
	}

	//$: console.log(shadowEl);
	$: if (shadowHeight && shadowEl) {
		if (shadowEl.style.height == 0) {
			//if (shadowEl.style.height == 0) ensures the bellow runs only once per grab of element to move
			shadowEl.style.height = `${shadowHeight + 18}px`;
			shadowEl.style.width = `${shadowWidth}px`;

			//put labelElClone in place of shadowEl
			// if (labelElClone) {
			// 	shadowEl.removeChild(labelElClone);
			// }
			labelElClone = labelEl.cloneNode(true);
			labelElClone.classList.remove('dnd-item');
			labelElClone.classList.add('border-2', 'border-accent');

			shadowEl.appendChild(labelElClone);
		}
	}
	function startDrag(e) {
		// preventing default to prevent lag on touch devices (because of the browser checking for screen scrolling)
		//e.preventDefault();
		dragDisabled = false;
	}
	function handleKeyDown(e) {
		if ((e.key === 'Enter' || e.key === ' ') && dragDisabled) dragDisabled = false;
	}
	const transformDraggedElement = (draggedEl, data, index) => {
		draggedEl
			.querySelector('.dnd-item')
			?.classList.add('bg-accent/25', 'border-2', 'border-accent');
	};
	//
	const dragDisabledConstantTest = true;
	let final_gqlArgObj_Store = getContext('final_gqlArgObj_Store');
	const handleChanged = () => {
		final_gqlArgObj_Store.regenerate_groupsAndfinal_gqlArgObj();
	};
</script>

{#if !node?.isMain}
	<div class=" grid   content-center  rounded-full ">
		<!-- svelte-ignore a11y-no-noninteractive-tabindex -->
		<div
			tabindex={dragDisabled ? 0 : -1}
			aria-label="drag-handle"
			class="  transition:all duration-500 bi bi-grip-vertical ml-2  -mr-1 text-lg rounded-l-md {node?.operator ==
				undefined || node?.isBond
				? 'text-base-content'
				: node?.operator == '_and'
				? 'text-primary'
				: 'text-accent-focus'} {node?.not ? ' bg-gradient-to-r from-base-300/100' : 'bg-error/0'}"
			style={dragDisabled ? 'cursor: grab' : 'cursor: grabbing'}
			on:mousedown={(e) => {
				// preventing default to prevent lag on touch devices (because of the browser checking for screen scrolling)
				e.preventDefault();

				dispatch('childrenStartDrag');
			}}
			on:touchstart={(e) => {
				// preventing default to prevent lag on touch devices (because of the browser checking for screen scrolling)
				e.preventDefault();

				dispatch('childrenStartDrag');
			}}
			on:keydown={handleKeyDown}
			on:contextmenu|preventDefault|stopPropagation={() => {
				if (!node?.isMain) {
					node.not = !node.not;
					handleChanged();
					dispatch('changed');
				}
			}}
		/>
	</div>{/if}

<div
	class=" w-full transition-all duration-500
	
	
	{node?.operator ? 'rounded-l-md bg-gradient-to-rxxx   border-l-[1px] my-1 shadow-sm' : ''} 
	
{node?.operator && node?.not ? 'border-dashed  ' : ''} 
{node.isBond
		? 'border-base-content'
		: node?.operator == '_and'
		? 'border-primary'
		: 'border-accent-focus '}


"
	on:contextmenu|preventDefault|stopPropagation={() => {
		if (!node?.isMain) {
			node.not = !node.not;
			handleChanged();
			dispatch('changed');
		}
	}}
	bind:this={labelEl}
	on:mousedown={() => {
		dragDisabled = true;
	}}
	on:touchstart={() => {
		dragDisabled = true;
	}}
	on:keydown={() => {
		dragDisabled = true;
	}}
>
	{#if node?.operator}
		<div class="flex ">
			<!-- svelte-ignore a11y-click-events-have-key-events -->
			<p
				style=""
				class="btn btn-xs btn-ghost px-2 pb-1 text-xs font-light transition-all duration-500  rounded-full  normal-case   {node.isBond
					? 'text-base-content'
					: node?.operator == '_and'
					? 'text-primary'
					: 'text-accent-focus'}"
				on:click={() => {
					if (node?.operator && !node?.isMain) {
						if (node?.operator == '_or') {
							node.operator = '_and';
						} else if (node?.operator == '_and' && !node?.isBond) {
							node.isBond = true;
						} else {
							node.isBond = false;
							node.operator = '_or';
						}
					}
					handleChanged();
					dispatch('changed');
				}}
			>
				{!node.isBond ? node.operator : 'bonded'}
			</p>
			<p class="grow" />
			{#if !node?.isMain}
				<!-- svelte-ignore a11y-click-events-have-key-events -->
				<p
					class="btn btn-xs btn-ghost {node.isBond
						? 'text-base-content'
						: node?.operator == '_and'
						? 'text-primary'
						: 'text-accent-focus'}"
					on:click={() => {
						alert('not yet implemented');
						console.log(
							'not yet implemented,implement here.Delete node and his items and items of his items recursively until the very end of the tree.'
						);
					}}
				>
					<i class="bi bi-trash-fill" />
				</p>{/if}
		</div>
	{:else}
		<div class="pr-2 rounded-box  w-full">
			<div
				class=" transition-color duration-500 rounded-box ringxxx  ring-1xxx    {node?.not
					? ' ring-errorxxx'
					: 'ring-error/0xxx'}"
			>
				<ActiveArgument
					on:contextmenuUsed={() => {
						if (!node?.isMain) {
							node.not = !node.not;
							handleChanged();
							dispatch('changed');
						}
					}}
					on:updateQuery
					on:inUseChanged={() => {}}
					activeArgumentData={node}
					{group}
				/>
			</div>
		</div>
	{/if}

	{#if node.hasOwnProperty('items')}
		<section
			class=" rounded-l-none {node?.items?.length == 0 ? 'pt-6' : ''} {node?.isMain
				? 'pb-10 border-l-2 border-l-transparent'
				: ' pb-2'} {node?.isMain
				? 'overflow-scroll overscroll-contain md:overscroll-auto h-[60vh] md:h-[80vh]'
				: 'min'} w-full"
			use:dndzone={{
				items: node.items,
				dragDisabled,
				flipDurationMs,
				transformDraggedElement,
				centreDraggedOnCursor: false,
				type
			}}
			on:consider={handleDndConsider}
			on:finalize={handleDndFinalize}
		>
			<!-- WE FILTER THE SHADOW PLACEHOLDER THAT WAS ADDED IN VERSION 0.7.4, filtering this way rather than checking whether 'nodes' have the id became possible in version 0.9.1 -->
			{#each node.items.filter((item) => {
				return item.id !== SHADOW_PLACEHOLDER_ITEM_ID;
			}) as item (item.id)}
				<div animate:flip={{ duration: flipDurationMs }} class="  flex   ">
					<svelte:self
						on:deleteSubNode={(e) => {
							deleteItem(e);
							//console.log(e.detail.id, node);
						}}
						{originalNodes}
						on:updateQuery
						{type}
						bind:nodes
						node={nodes[item.id]}
						on:changed
						{availableOperators}
						on:childrenStartDrag={startDrag}
						{group}
					/>
				</div>
			{/each}
		</section>
	{/if}
</div>
{#if node.id == SHADOW_PLACEHOLDER_ITEM_ID}
	<div class=" ml-8 h-0     top-0 left-0 visible" id="shadowEl" bind:this={shadowEl} />
{/if}

<style>
</style>
