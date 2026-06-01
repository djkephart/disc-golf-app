<script>
  import CourseCard from '$lib/components/CourseCard.svelte';
  import { courses } from '$lib/data/courses.js';

  let search = $state('');
  let sortBy = $state('rating');
  let difficulty = $state('All');

  const difficulties = ['All', 'Beginner', 'Intermediate', 'Advanced'];

  const sortOptions = [
    { value: 'rating', label: '⭐ Rating' },
    { value: 'holes', label: '⛳ Holes' },
    { value: 'name', label: '🔤 Name' },
    { value: 'difficulty', label: '📊 Difficulty' }
  ];

  const filtered = $derived(() => {
    let result = [...courses];

    if (search.trim()) {
      const q = search.toLowerCase();
      result = result.filter(c =>
        c.name.toLowerCase().includes(q) ||
        c.location.toLowerCase().includes(q)
      );
    }

    if (difficulty !== 'All') {
      result = result.filter(c => c.difficulty === difficulty);
    }

    if (sortBy === 'rating') result.sort((a, b) => b.rating - a.rating);
    else if (sortBy === 'holes') result.sort((a, b) => b.holes - a.holes);
    else if (sortBy === 'name') result.sort((a, b) => a.name.localeCompare(b.name));
    else if (sortBy === 'difficulty') {
      const order = { Beginner: 0, Intermediate: 1, Advanced: 2 };
      result.sort((a, b) => order[a.difficulty] - order[b.difficulty]);
    }

    return result;
  });
</script>

<section class="px-8 py-12 w-full">
  <p class="text-green-400 text-sm font-semibold tracking-widest uppercase mb-2">Explore</p>
  <h1 class="text-4xl font-bold text-white mb-2">Courses</h1>
  <p class="text-gray-400 mb-8">Browse disc golf courses in the Kansas City metro.</p>

  <!-- Search bar -->
  <div class="relative mb-6">
    <span class="absolute left-4 top-1/2 -translate-y-1/2 text-gray-500">🔍</span>
    <input
      type="text"
      placeholder="Search by name or location..."
      bind:value={search}
      class="w-full bg-[#1a1d27] border border-white/10 rounded-full px-12 py-3 text-white placeholder-gray-500 focus:outline-none focus:border-green-500/50 transition-colors duration-200"
    />
    {#if search}
      <button
        onclick={() => search = ''}
        class="absolute right-4 top-1/2 -translate-y-1/2 text-gray-500 hover:text-white transition-colors"
      >✕</button>
    {/if}
  </div>

  <!-- Difficulty filter -->
  <div class="flex gap-2 mb-6 flex-wrap">
    {#each difficulties as d}
      <button
        onclick={() => difficulty = d}
        class="px-4 py-2 rounded-full text-sm font-medium transition-all duration-200
          {difficulty === d
            ? 'bg-green-500 text-black'
            : 'bg-[#1a1d27] border border-white/10 text-gray-400 hover:text-white hover:border-white/30'}"
      >
        {d}
      </button>
    {/each}
  </div>

  <!-- Sort options -->
  <div class="flex items-center gap-3 mb-8 flex-wrap">
    <span class="text-gray-500 text-sm">Sort by:</span>
    {#each sortOptions as option}
      <button
        onclick={() => sortBy = option.value}
        class="px-3 py-1.5 rounded-full text-sm transition-all duration-200
          {sortBy === option.value
            ? 'bg-white/10 text-white border border-white/20'
            : 'text-gray-500 hover:text-gray-300'}"
      >
        {option.label}
      </button>
    {/each}
  </div>

  <!-- Results count -->
  <p class="text-gray-500 text-sm mb-6">
    {filtered().length} course{filtered().length !== 1 ? 's' : ''} found
  </p>

  <!-- Course grid -->
  {#if filtered().length > 0}
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
      {#each filtered() as course}
        <CourseCard {course} />
      {/each}
    </div>
  {:else}
    <div class="text-center py-20">
      <p class="text-4xl mb-4">🥏</p>
      <p class="text-white font-semibold mb-2">No courses found</p>
      <p class="text-gray-500 text-sm">Try adjusting your search or filters</p>
    </div>
  {/if}
</section>