<script>
  import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

  export let swindows = [];
  export let activeWindowId;

  function selectWindow(id) {
    dispatch('selectWindow', { id });
  }

  function runApp() {
    let id = Math.floor(Math.random() * 10000);
    dispatch('runApp', { id });
  }
</script>

<footer>
  <div class="start-button">
    <button on:click="{runApp}">
      <img src="/assets/logo.png" alt="Start icon" />
      Start
    </button>
  </div>
  {#each swindows as swindow}
    <div class="open-windows">
      <button
        class:active="{swindow.id === activeWindowId}"
        on:click="{selectWindow(swindow.id)}"
      >
        {swindow.title}
      </button>
    </div>
  {/each}
</footer>

<style>
  footer {
    position: absolute;
    bottom: 0;
    left: 0;
    display: flex;
    width: 100vw;
    padding: 2px;
    background: silver;
    box-shadow: inset -1px -1px #0a0a0a,inset 1px 1px #dfdfdf,inset -2px -2px grey,inset 2px 2px #fff;
  }

  footer .open-windows button {
    outline: 0;
    margin-right: 2px;
    width: 200px;
    text-align: left;
  }

  .start-button {
    margin-right: 5px;
  }

  .start-button button {
    min-width: 0;
    width: 55px;
    text-align: left;
    font-weight: bold;
    padding: 0 4px;
  }

  .start-button img {
    height: 16px;
    vertical-align: bottom;
  }
</style>