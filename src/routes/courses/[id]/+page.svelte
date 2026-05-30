<script>
  import { page } from '$app/stores';
  import { courses } from '$lib/data/courses.js';

  const id = Number($page.params.id);
  const course = courses.find(c => c.id === id);
</script>

{#if course}
<section class="px-6 py-12 max-w-3xl mx-auto">
  <a href="/courses" class="text-green-400 text-sm hover:text-green-300 transition-colors duration-200">← Back to Courses</a>

  <h1 class="text-4xl font-bold text-white mt-4 mb-1">{course.name}</h1>
  <p class="text-green-400 mb-8">📍 {course.location}</p>

  <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mb-8">
    <div class="bg-[#1a1d27] border border-white/10 rounded-xl p-4 text-center">
      <p class="text-gray-400 text-xs uppercase tracking-widest mb-1">Holes</p>
      <p class="text-white font-bold text-xl">{course.holes}</p>
    </div>
    <div class="bg-[#1a1d27] border border-white/10 rounded-xl p-4 text-center">
      <p class="text-gray-400 text-xs uppercase tracking-widest mb-1">Par</p>
      <p class="text-white font-bold text-xl">{course.par}</p>
    </div>
    <div class="bg-[#1a1d27] border border-white/10 rounded-xl p-4 text-center">
      <p class="text-gray-400 text-xs uppercase tracking-widest mb-1">Length</p>
      <p class="text-white font-bold text-xl">{course.length}</p>
    </div>
    <div class="bg-[#1a1d27] border border-white/10 rounded-xl p-4 text-center">
      <p class="text-gray-400 text-xs uppercase tracking-widest mb-1">Rating</p>
      <p class="text-white font-bold text-xl">⭐ {course.rating}</p>
    </div>
  </div>

  <div class="flex flex-col gap-6">
    <div class="bg-[#1a1d27] border border-white/10 rounded-2xl p-6">
      <h2 class="text-lg font-semibold text-white mb-3">About This Course</h2>
      <p class="text-gray-400">{course.highlights}</p>
    </div>

    <div class="bg-[#1a1d27] border border-white/10 rounded-2xl p-6">
      <h2 class="text-lg font-semibold text-white mb-3">Details</h2>
      <ul class="text-gray-400 flex flex-col gap-2">
        <li>🌲 Terrain: {course.terrain}</li>
        <li>📊 Difficulty: {course.difficulty}</li>
      </ul>
    </div>

    <div class="bg-[#1a1d27] border border-white/10 rounded-2xl p-6">
      <h2 class="text-lg font-semibold text-white mb-3">Amenities</h2>
      <ul class="text-gray-400 flex flex-col gap-2">
        {#each course.amenities as amenity}
          <li>✅ {amenity}</li>
        {/each}
      </ul>
    </div>

    <a
      href="/scorecard?course={course.id}"
      class="bg-green-500 text-black font-semibold px-6 py-3 rounded-full text-center hover:bg-green-400 transition-colors duration-200"
    >
      Log a Round at This Course →
    </a>
  </div>
</section>
{:else}
<section class="px-6 py-12 max-w-3xl mx-auto">
  <p class="text-gray-400">Course not found.</p>
  <a href="/courses" class="text-green-400 hover:text-green-300 mt-4 inline-block">← Back to Courses</a>
</section>
{/if}