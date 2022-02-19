<script>
	import Taskbar from './desktop/Taskbar.svelte';
	import Swindow from './desktop/Swindow.svelte'

	let dragOrigin;
	let mousePosition;

	$: dragDelta = dragOrigin ? { x: dragOrigin.x - mousePosition.x, y: dragOrigin.y - mousePosition.y } : null;
	
	let activeWindowId = 1;
	let heldWindowId;
	let heldWindowOrigin;

	let swindows = [
		{
			id: 1,
			x: 200,
			y: 200,
			width: 200,
			height: 100,
			title: 'Example 1'
		},
		{
			id: 2,
			x: 300,
			y: 150,
			width: 200,
			height: 100,
			title: 'Example 2'
		}
	];

	function selectWindow(event) {
		activeWindowId = event.detail.id;
	}

	function closeWindow(event) {
		swindows = swindows.filter((swindow) => swindow.id !== event.detail.id);
	}

	function initWindowMove(event) {
		heldWindowId = event.detail.id;
		heldWindowOrigin = { x: event.detail.x, y: event.detail.y };
	}

	function endWindowMove() {
		heldWindowId = null;
		heldWindowOrigin = null;
	}

	function initDrag(event) {
		dragOrigin = { x: event.clientX, y: event.clientY };
	}

	function endDrag() {
		dragOrigin = null;
		endWindowMove();
	}

	function moveMouse(event) {
		mousePosition = { x: event.clientX, y: event.clientY };
		if (heldWindowId) {
			moveWindow(heldWindowId)
		}
	}

	function moveWindow(id) {
		swindows = swindows.map((swindow) => {
			if (swindow.id != id) {
				return swindow;
			} else {
				let newX = heldWindowOrigin.x - dragDelta.x;
				let newY = heldWindowOrigin.y - dragDelta.y;
				return {...swindow, x: newX, y: newY }
			}
		});
	}
</script>

<main on:mousemove="{moveMouse}" on:mousedown="{initDrag}" on:mouseup="{endDrag}">
	{#each swindows as swindow}
		<Swindow
			{...swindow}
			active="{swindow.id === activeWindowId}"
			on:close="{closeWindow}"
			on:select="{selectWindow}"
			on:move="{initWindowMove}"
		/>
	{/each}
	<Taskbar {swindows} />
</main>

<style>
	main {
		position: relative;
		width: 100vw;
		height: 100vh;
		margin: 0;
		padding: 0;
		background-color: darkcyan;
		user-select: none;
		overflow: hidden;
	}
</style>