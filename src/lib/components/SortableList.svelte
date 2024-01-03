<script lang="ts" context="module">
	export interface IDragging {
		isActive: boolean;
		itemIndex: number | null;
	}

	export interface IItemClone {
		text: string;
		width: number;
		height: number;
		left: number;
		top: number;
		clientX: number;
		clientY: number;
		transform?: string;
	}
</script>

<script lang="ts">
	import { setContext } from 'svelte';
	import { writable } from 'svelte/store';
	import SortableItem from './SortableItem.svelte';

	const dragging = writable<IDragging>({
		isActive: false,
		itemIndex: null,
	});
	const itemClone = writable<IItemClone | null>(null);
	setContext('dragging', dragging);
	setContext('itemClone', itemClone);

	function handleMouseMove(event: MouseEvent) {
		if ($dragging.isActive) {
			$itemClone = {
				...$itemClone,
				transform: `translate3d(${event.clientX - ($itemClone?.clientX || 0)}px, ${
					event.clientY - ($itemClone?.clientY || 0)
				}px, 0)`,
			} as IItemClone;
		}
	}

	function handleMouseUp(event: MouseEvent) {
		$itemClone = {
			...$itemClone,
			transform: 'translate3d(0, 0, 0)',
		} as IItemClone;
		$dragging = { isActive: false, itemIndex: null };
	}

	$: itemCloneStyles =
		!!$itemClone &&
		[
			`width: ${$itemClone.width}px`,
			`height: ${$itemClone.height}px`,
			`left: ${$itemClone.left}px`,
			`top: ${$itemClone.top}px`,
			`transform: ${$itemClone.transform}`,
		].join(';');
</script>

<svelte:document on:mousemove={handleMouseMove} on:mouseup={handleMouseUp} />

<ul class="sortable-list">
	<slot />
	{#if $dragging.itemIndex !== null && $itemClone}
		<SortableItem class="sortable-item--ghost" style={itemCloneStyles}>
			{$itemClone.text}
		</SortableItem>
	{/if}
</ul>

<style>
	.sortable-list {
		box-sizing: border-box;
		padding: 0;
	}
</style>
