<script lang="ts">
  import { goto } from '$app/navigation';
  import { page } from '$app/stores';

  function handleClick() {
    if ($page.url.pathname === '/') {
      window.location.reload();
    } else {
      goto('/');
    }
  }
</script>

<button 
  class="btn btn-success rounded-circle position-fixed shadow home-button" 
  on:click={handleClick} 
  aria-label="Go to Home or Refresh"
>
  <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" class="bi bi-house-door-fill" viewBox="0 0 16 16">
    <path d="M6.5 14.5v-3.505c0-.245.25-.495.5-.495h2c.25 0 .5.25.5.5v3.5a.5.5 0 0 0 .5.5h4a.5.5 0 0 0 .5-.5v-7a.5.5 0 0 0-.146-.354L13 5.793V2.5a.5.5 0 0 0-.5-.5h-1a.5.5 0 0 0-.5.5v1.293L8.354 1.146a.5.5 0 0 0-.708 0l-6 6A.5.5 0 0 0 1.5 7.5v7a.5.5 0 0 0 .5.5h4a.5.5 0 0 0 .5-.5z"/>
  </svg>
</button>

<style>
  .home-button {
    top: 20px;
    right: 20px;
    z-index: 1000;
    width: 60px;
    height: 60px;
    transition: all 0.3s ease;
  }

  .home-button:hover {
    transform: scale(1.1);
    box-shadow: 0 6px 8px rgba(0, 0, 0, 0.15) !important;
  }
</style>
