<script>
	import Taskbar from './components/Taskbar.svelte';
	import Swindow from './components/Swindow.svelte'

	let dragOrigin;
	let mousePosition;

	$: dragDelta = dragOrigin ? { x: dragOrigin.x - mousePosition.x, y: dragOrigin.y - mousePosition.y } : null;
	
	let activeWindowId;
	let heldWindowId;
	let heldWindowOrigin;

	let swindows = [];

	function selectWindow(event) {
		activeWindowId = event.detail.id;
	}

	function closeWindow(event) {
		swindows = swindows.filter((swindow) => swindow.id !== event.detail.id);
		activeWindowId = swindows.map((s) => s.id)[swindows.length - 1];
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

	function runApp(event) {
		let newWindow = {
				id: swindows.length > 0 ? (Math.max(...swindows.map((s) => s.id))) + 1 : 0,
				x: 10 + Math.floor(Math.random() * 100),
				y: 10 + Math.floor(Math.random() * 100),
				width: 300 + Math.floor(Math.random() * 200),
				height: 200 + Math.floor(Math.random() * 200),
				title: `App ${event.detail.id}`
		}

		swindows = swindows.concat([newWindow]);
		activeWindowId = newWindow.id;
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
	<Taskbar {swindows} {activeWindowId} on:selectWindow="{selectWindow}" on:runApp="{runApp}" />
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