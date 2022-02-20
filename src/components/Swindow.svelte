<script>
  import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

  export let id;
  export let title = 'Unnamed';
  export let x = 100;
  export let y = 100;
  export let width = 200;
  export let height = 100;
  export let active;

  function requestClose() {
    dispatch('close', { id });
  }

  function requestMove(event) {
    dispatch('move', { id, x, y });
  }

  function requestSelect() {
    dispatch('select', { id });
  }
</script>

<div class="window" on:mousedown="{requestSelect}" class:active style="width: {width}px; height: {height}px; top: {y}px; left: {x}px;">
  <div class="title-bar" class:inactive="{!active}" on:mousedown="{requestMove}">
    <div class="title-bar-text">
      {title}
    </div>

    <div class="title-bar-controls">
      <button aria-label="Minimize"></button>
      <button aria-label="Maximize"></button>
      <button aria-label="Close" on:click="{requestClose}"></button>
    </div>
  </div>
  <div class="window-body">
    <slot></slot>
  </div>
</div>

<style>
  .window {
    position: absolute;
  }

  .window.active {
    z-index: 1000;
  }
</style>