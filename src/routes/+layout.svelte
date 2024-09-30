<script lang="ts">
  import { browser } from '$app/environment';
  import { onMount } from 'svelte';
  import 'bootstrap/dist/css/bootstrap.min.css';

  onMount(async () => {
    if (browser) {
      const bootstrap = await import('bootstrap');
    }
  });
</script>

<main>
  <slot />
</main>
