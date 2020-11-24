<script>
  import { createEventDispatcher } from 'svelte';
  export let selected = false;
  export let sortAsc;

  const dispatch = createEventDispatcher();

  function selectField() {
    dispatch('select');
  }
</script>

<style>
  .down {
    transform: scaleY(-1);
  }

  span {
    position: relative;

  }

  th {
    text-align: left;
    padding: 0 1rem;
  }

  svg {
    position: absolute;
    left: -15px;
    width: 10px;
    top: 2px;
  }
  .hide {
    display: none;
  }
</style>

<th on:click={() => selectField()}>
  <span class:hide={!selected}>
    <svg
      aria-hidden="true"
      data-prefix="fas"
      data-icon="caret-up"
      xmlns="http://www.w3.org/2000/svg"
      viewBox="0 0 320 512"
      class:down={sortAsc}>
      <path
        fill="currentColor"
        d="M288.662 352H31.338c-17.818 0-26.741-21.543-14.142-34.142l128.662-128.662c7.81-7.81 20.474-7.81 28.284 0l128.662 128.662c12.6 12.599 3.676 34.142-14.142 34.142z" />
    </svg>
  </span>
  <slot />
</th>
