<script>
  export let isVisible = false;
  export let movies = [];
  let selectedMovies = [];
  let message = '';

  function close() {
    isVisible = false;
    selectedMovies = [];
    message = '';
    dispatch('close');
  }

  function toggleSelection(movie) {
    if (selectedMovies.includes(movie)) {
      selectedMovies = selectedMovies.filter(m => m !== movie);
    } else if (selectedMovies.length < 5) {
      selectedMovies = [...selectedMovies, movie];
    }
  }

  function submitSelection() {
    const originalContent = [
      "Stranger Things", "The Witcher", "Bridgerton", "Money Heist (La Casa de Papel)",
      "Outer Banks", "Sex Education", "The Umbrella Academy", "To All the Boys I've Loved Before (movie series)"
    ];

    const originalCount = selectedMovies.filter(movie => originalContent.includes(movie)).length;

    if (originalCount >= 3) {
      message = `
        <h3>You're a True Netflix Original Fan!</h3>
        <p>Great choices! It looks like you are attracted by the original content that Netflix has been heavily investing in. These exclusive series and movies are key strategies for Netflix to stand out in the competitive streaming market. Keep enjoying the unique stories that only Netflix can offer!</p>
      `;
    } else {
      message = `
        <h3>Exploring Beyond the Originals!</h3>
        <p>Interesting picks! While you enjoy a variety of movies, you might want to dive deeper into Netflix's original content. These unique series and movies have been pivotal in Netflix's strategy to compete with other streaming services. Discover more original stories and see what makes Netflix a giant in the industry!</p>
      `;
    }

    /* setTimeout(() => {
      close();
      dispatch('submit');
    }, 10000); // Close after 10 seconds */
  }

  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();
</script>

{#if isVisible}
  <div class="popup">
    <div class="popup-content">
      <button class="close-button" on:click={close}>X</button>
      {#if message}
        {@html message}
      {:else}
        <p>Please select 5 movies that you love or have seen before (out of 16 movies):</p>
        <div class="movie-list">
          {#each movies as movie}
            <div class="movie-item" on:click={() => toggleSelection(movie)}>
              <input type="checkbox" checked={selectedMovies.includes(movie)} disabled={selectedMovies.length >= 5 && !selectedMovies.includes(movie)} />
              {movie}
            </div>
          {/each}
        </div>
        <button on:click={submitSelection} disabled={selectedMovies.length !== 5}>Submit</button>
      {/if}
    </div>
  </div>
{/if}

<style>
  .popup {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1000; /* Ensure the popup is on top */
    overflow: hidden; /* Ensure no overflow within the popup */
  }
  .popup-content {
    position: relative;
    background: white;
    padding: 2em;
    border-radius: 8px;
    max-width: 400px;
    width: 90%;
  }
  .movie-list {
    max-height: 200px;
    overflow-y: auto;
    margin: 1em 0;
  }
  .movie-item {
    display: flex;
    align-items: center;
    cursor: pointer;
    margin: 0.5em 0;
  }
  .movie-item input {
    margin-right: 0.5em;
  }
  button:disabled {
    background: #ccc;
    cursor: not-allowed;
  }
  .close-button {
    position: absolute;
    top: 10px;
    right: 10px;
    background: none;
    border: none;
    font-size: 1.5em;
    cursor: pointer;
  }
</style>