<script>
  let { course } = $props();
</script>

<a href="/courses/{course.id}" class="bg-[#1a1d27] border border-white/10 rounded-2xl p-6 flex flex-col gap-3 hover:border-green-500/50 transition-colors duration-200">
  <h2 class="text-lg font-bold text-white">{course.name}</h2>
  <p class="text-green-400 text-sm">📍 {course.location}</p>
  <p class="text-gray-400 text-sm">{course.description}</p>
  <div class="flex gap-4 mt-2 text-sm text-gray-500">
    <span>⛳ {course.holes} holes</span>
    <span>📊 {course.difficulty}</span>
    <span>⭐ {course.rating}</span>
  </div>
  <p class="text-green-400 text-sm mt-auto">View Course →</p>
</a>