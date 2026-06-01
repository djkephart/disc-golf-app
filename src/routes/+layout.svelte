<script>
  import Navbar from '$lib/components/Navbar.svelte';
  import Footer from '$lib/components/Footer.svelte';
  import { fade } from 'svelte/transition';
  import { page } from '$app/stores';
  import '../app.css';

  let { children } = $props();
</script>

<Navbar />

<main class="min-h-screen bg-[#0f1117] w-full">
  {#key $page.url.pathname}
    <div in:fade={{ duration: 150, delay: 100 }}>
      {@render children()}
    </div>
  {/key}
</main>

<Footer />