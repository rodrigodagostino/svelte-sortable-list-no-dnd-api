<script lang="ts">
	import { getContext } from 'svelte';
	import type { Writable } from 'svelte/store';
	import type { IDragging, IItemClone } from './SortableList.svelte';

	export let index: number | undefined = undefined;

	let itemRef: HTMLLIElement;

	const dragging = getContext<Writable<IDragging>>('dragging');
	const itemClone = getContext<Writable<IItemClone | null>>('itemClone');

	function handleMouseDown(event: MouseEvent) {
		const currTarget = event.currentTarget as HTMLLIElement;

		$dragging = { isActive: true, itemIndex: index ?? null };
		$itemClone = {
			text: currTarget?.textContent || '',
			width: currTarget?.getBoundingClientRect().width,
			height: currTarget?.getBoundingClientRect().height,
			left: currTarget?.getBoundingClientRect().left,
			top: currTarget?.getBoundingClientRect().top,
			clientX: event.clientX,
			clientY: event.clientY,
		};
	}
</script>

<li
	bind:this={itemRef}
	class="sortable-item {$$restProps.class ? $$restProps.class : ''}"
	class:is-dragging={$dragging.itemIndex === index}
	style={$$restProps.style ? $$restProps.style : ''}
	tabindex="0"
	on:mousedown={handleMouseDown}
>
	<slot />
</li>

<style lang="scss">
	.sortable-item {
		box-sizing: border-box;
		list-style: none;
		cursor: grab;
		user-select: none;

		&--ghost {
			position: fixed;
			cursor: grabbing;
		}

		&.is-dragging {
			visibility: hidden;
		}
	}
</style>
