<script>
  import CourseCard from '$lib/components/CourseCard.svelte';
  import { courses } from '$lib/data/courses.js';
</script>

<section class="px-6 py-12 max-w-4xl mx-auto">
  <p class="text-green-400 text-sm font-semibold tracking-widest uppercase mb-2">Explore</p>
  <h1 class="text-4xl font-bold text-white mb-2">Courses</h1>
  <p class="text-gray-400 mb-10">Browse disc golf courses in your area.</p>

  <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
    {#each courses as course}
      <CourseCard {course} />
    {/each}
  </div>
</section>