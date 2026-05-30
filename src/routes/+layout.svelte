<script>import Navbar from '$lib/components/Navbar.svelte';
import Footer from '$lib/components/Footer.svelte';
import '../app.css';

let { children } = $props();</script>

<Navbar></Navbar>

<main class="min-h-screen bg-[#0f1117] w-full">
  {@render children()}
</main>

<Footer></Footer>
