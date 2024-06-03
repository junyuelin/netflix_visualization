<script>
  import { onMount } from 'svelte';
  import PopupQuestion from '../components/PopupQuestion.svelte';

  let showPopup = false;
  let movies = [];

  const originalContent = [
      "Stranger Things", "The Witcher", "Bridgerton", "Money Heist (La Casa de Papel)",
      "Outer Banks", "Sex Education", "The Umbrella Academy", "To All the Boys I've Loved Before (movie series)"
  ];

  const licensedContent = [
      "Friends", "The Office (US)", "Gossip Girl", "Breaking Bad",
      "Avatar: The Last Airbender", "Gilmore Girls", "The Vampire Diaries", "The Walking Dead"
  ];

  // Mix original and licensed content
  movies = originalContent.flatMap((orig, i) => [orig, licensedContent[i]]);

  onMount(() => {
    // Always allow the popup to show on refresh
    sessionStorage.removeItem('popupShown');

    window.addEventListener('scroll', handleScroll);

    return () => {
      window.removeEventListener('scroll', handleScroll);
    };
  });

  function handleScroll() {
    const scrollY = window.scrollY;
    const targetScrollPosition = 500; // Change this value to the desired scroll position

    if (scrollY >= targetScrollPosition && !showPopup && !sessionStorage.getItem('popupSubmitted')) {
      showPopup = true;
      sessionStorage.setItem('popupShown', 'true'); // Mark as shown for this session
    }
  }

  function closePopup() {
    showPopup = false;
    sessionStorage.setItem('popupSubmitted', 'true'); // Mark as submitted for this session
  }

  function handleSubmit() {
    showPopup = false;
    sessionStorage.setItem('popupSubmitted', 'true'); // Mark as submitted for this session
  }
</script>

<PopupQuestion isVisible={showPopup} movies={movies} on:close={closePopup} on:submit={handleSubmit} />