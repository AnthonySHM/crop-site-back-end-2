<script lang="ts">
  import { page } from '$app/stores';
  import { onMount } from 'svelte';
  import HomeButton from '$lib/HomeButton.svelte';
  import { Carousel } from 'bootstrap';

  let crop: any = null;
  let activeTab = 'overview';
  let searchQuery = '';
  let carousels: { [key: string]: typeof Carousel } = {};

  onMount(async () => {
    const cropId = $page.params.id;
    try {
      const response = await fetch(`http://localhost:3000/api/crops/${cropId}`);
      if (!response.ok) {
        throw new Error('Crop not found');
      }
      crop = await response.json();
      initCarousels();
    } catch (error) {
      console.error('Error fetching crop details:', error);
      crop = {
        id: cropId,
        name: 'Crop Not Found',
        image: 'https://example.com/images/default-crop.jpg',
        overview: 'Sorry, we couldn\'t find information for this crop.',
        planting: 'N/A',
        care: 'N/A',
        harvest: 'N/A',
        economics: 'N/A',
      };
    }
  });

  function setActiveTab(tab: string) {
    activeTab = tab;
    setTimeout(() => {
      if (carousels[tab]) {
        carousels[tab].dispose();
      }
      initCarousel(tab);
    }, 0);
  }

  function initCarousels() {
    ['overview', 'planting', 'care', 'harvest', 'economics'].forEach(initCarousel);
  }

  function initCarousel(tab: string) {
    const carouselEl = document.querySelector(`#${tab}Carousel`);
    if (carouselEl) {
      carousels[tab] = new Carousel(carouselEl, {
        interval: 5000,
        wrap: true
      });
    }
  }

  interface Video {
    title: string;
    url: string;
    description?: string;
  }

  function filterVideos(videos: Video[]) {
    return videos.filter(video => 
      video.title.toLowerCase().includes(searchQuery.toLowerCase())
    );
  }

  function getYoutubeId(url: string): string | null {
    const regExp = /^.*(youtu.be\/|v\/|u\/\w\/|embed\/|watch\?v=|\&v=)([^#\&\?]*).*/;
    const match = url.match(regExp);
    return (match && match[2].length === 11) ? match[2] : null;
  }

  $: youtubeId = crop && activeTab ? getYoutubeId(crop[activeTab + 'Videos'][0]?.url) : null;
</script>

<HomeButton />

<div class="container mt-5 pt-4">
  {#if crop}
    <h1 class="mb-4">{crop.name}</h1>
    <div class="row">
      <div class="col-md-8">
        <ul class="nav nav-tabs mb-3">
          <li class="nav-item">
            <button class="nav-link" class:active={activeTab === 'overview'} on:click={() => setActiveTab('overview')}>Overview</button>
          </li>
          <li class="nav-item">
            <button class="nav-link" class:active={activeTab === 'planting'} on:click={() => setActiveTab('planting')}>Planting</button>
          </li>
          <li class="nav-item">
            <button class="nav-link" class:active={activeTab === 'care'} on:click={() => setActiveTab('care')}>Care</button>
          </li>
          <li class="nav-item">
            <button class="nav-link" class:active={activeTab === 'harvest'} on:click={() => setActiveTab('harvest')}>Harvest</button>
          </li>
          <li class="nav-item">
            <button class="nav-link" class:active={activeTab === 'economics'} on:click={() => setActiveTab('economics')}>Economics</button>
          </li>
        </ul>

        <div class="tab-content">
          {#each ['overview', 'planting', 'care', 'harvest', 'economics'] as tab}
            <div class="tab-pane fade" class:show={activeTab === tab} class:active={activeTab === tab}>
              <div id="{tab}Carousel" class="carousel slide mb-4" data-bs-ride="carousel">
                <div class="carousel-inner">
                  {#if crop[tab + 'Images'] && crop[tab + 'Images'].length > 0}
                    {#each crop[tab + 'Images'] as image, index}
                      <div class="carousel-item" class:active={index === 0}>
                        <img src={image.url} class="d-block w-100" alt={image.caption} style="max-height: 400px; object-fit: cover;">
                        <div class="carousel-caption d-none d-md-block">
                          <p>{image.caption}</p>
                        </div>
                      </div>
                    {/each}
                  {:else}
                    <div class="carousel-item active">
                      <img src={crop.image} class="d-block w-100" alt={crop.name} style="max-height: 400px; object-fit: cover;">
                    </div>
                  {/if}
                </div>
                <button class="carousel-control-prev" type="button" data-bs-target="#{tab}Carousel" data-bs-slide="prev">
                  <span class="carousel-control-prev-icon" aria-hidden="true"></span>
                  <span class="visually-hidden">Previous</span>
                </button>
                <button class="carousel-control-next" type="button" data-bs-target="#{tab}Carousel" data-bs-slide="next">
                  <span class="carousel-control-next-icon" aria-hidden="true"></span>
                  <span class="visually-hidden">Next</span>
                </button>
              </div>
              <p>{crop[tab]}</p>
            </div>
          {/each}
        </div>
      </div>
      <div class="col-md-4">
        <div class="mb-3">
          <input type="text" class="form-control" placeholder="Search videos" bind:value={searchQuery}>
        </div>
        <div class="video-scroll" style="height: 500px; overflow-y: auto;">
          {#each filterVideos(crop[activeTab + 'Videos']) as video}
            <div class="card mb-3">
              <div class="card-body">
                <h5 class="card-title">{video.title}</h5>
                {#if video.description}
                  <p class="card-text">{video.description}</p>
                {/if}
                {#if youtubeId}
                  <iframe
                    class="w-100"
                    src={`https://www.youtube.com/embed/${youtubeId}`}
                    title="YouTube video player"
                  ></iframe>
                {:else}
                  <p>Invalid YouTube URL</p>
                {/if}
              </div>
            </div>
          {/each}
        </div>
      </div>
    </div>
  {:else}
    <div class="alert alert-info">Loading crop details...</div>
  {/if}
</div>