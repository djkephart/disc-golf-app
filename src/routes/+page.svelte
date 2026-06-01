<script>
  import { courses } from '$lib/data/courses.js';
  import { discs } from '$lib/data/discs.js';

  const featuredCourses = courses.filter(c => c.rating >= 4.5).slice(0, 3);
  const spotlightDiscs = discs.filter(d => ['Destroyer', 'Buzzz', 'Judge', 'Aviar', 'Zeus'].includes(d.name));

  const stats = [
    { value: courses.length, label: 'KC Courses' },
    { value: discs.length, label: 'Discs in Database' },
    { value: [...new Set(discs.map(d => d.brand))].length, label: 'Brands' },
    { value: '18+', label: 'Holes Tracked' }
  ];

  function stabilityColor(stability) {
    if (stability === 'Very Understable') return 'text-blue-400';
    if (stability === 'Understable') return 'text-cyan-400';
    if (stability === 'Neutral') return 'text-green-400';
    if (stability === 'Overstable') return 'text-orange-400';
    if (stability === 'Very Overstable') return 'text-red-400';
    return 'text-gray-400';
  }

  function typeIcon(type) {
    if (type === 'Distance Driver') return '💨';
    if (type === 'Fairway Driver') return '🎯';
    if (type === 'Midrange') return '🔄';
    if (type === 'Putter') return '🥅';
    return '🥏';
  }

  function difficultyColor(difficulty) {
    if (difficulty === 'Beginner') return 'text-green-400 bg-green-400/10 border-green-400/20';
    if (difficulty === 'Intermediate') return 'text-yellow-400 bg-yellow-400/10 border-yellow-400/20';
    if (difficulty === 'Advanced') return 'text-red-400 bg-red-400/10 border-red-400/20';
    return 'text-gray-400';
  }
</script>

<!-- Hero -->
<section class="w-full px-8 py-28 flex flex-col items-center text-center relative overflow-hidden">
  <div class="absolute inset-0 bg-gradient-to-b from-green-900/20 to-transparent pointer-events-none"></div>
  <div class="absolute inset-0 bg-[radial-gradient(ellipse_at_top,_#166534_0%,_transparent_60%)] opacity-20 pointer-events-none"></div>

  <p class="text-green-400 text-sm font-semibold tracking-widest uppercase mb-4 relative">Kansas City Disc Golf</p>
  <h1 class="text-6xl md:text-7xl font-bold text-white mb-6 leading-tight relative">
    Find Your Next<br/>
    <span class="text-green-400">Round</span>
  </h1>
  <p class="text-lg text-gray-400 max-w-xl mb-10 relative">
    Discover KC metro courses, explore the disc database, track your scores, and build your virtual bag.
  </p>
  <div class="flex gap-4 relative flex-wrap justify-center">
    <a href="/courses" class="bg-green-500 text-black font-semibold px-8 py-3 rounded-full text-lg hover:bg-green-400 transition-colors duration-200">
      Browse Courses
    </a>
    <a href="/discs" class="border border-white/20 text-white font-semibold px-8 py-3 rounded-full text-lg hover:bg-white/10 transition-colors duration-200">
      Disc Database
    </a>
  </div>
</section>

<!-- Stats bar -->
<section class="w-full px-8 py-8 border-y border-white/10 bg-[#1a1d27]">
  <div class="grid grid-cols-2 md:grid-cols-4 gap-6 text-center">
    {#each stats as stat}
      <div>
        <p class="text-3xl font-bold text-green-400">{stat.value}</p>
        <p class="text-gray-500 text-sm mt-1">{stat.label}</p>
      </div>
    {/each}
  </div>
</section>

<!-- Featured Courses -->
<section class="w-full px-8 py-16">
  <div class="flex items-end justify-between mb-8">
    <div>
      <p class="text-green-400 text-sm font-semibold tracking-widest uppercase mb-2">Top Rated</p>
      <h2 class="text-3xl font-bold text-white">Featured Courses</h2>
    </div>
    <a href="/courses" class="text-green-400 text-sm hover:text-green-300 transition-colors duration-200">View all →</a>
  </div>

  <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
    {#each featuredCourses as course}
      <a href="/courses/{course.id}" class="bg-[#1a1d27] border border-white/10 rounded-2xl p-6 flex flex-col gap-3 hover:border-green-500/50 transition-colors duration-200">
        <div class="flex items-start justify-between">
          <h3 class="text-white font-bold text-lg leading-tight">{course.name}</h3>
          <span class="text-yellow-400 text-sm ml-2 shrink-0">⭐ {course.rating}</span>
        </div>
        <p class="text-green-400 text-sm">📍 {course.location}</p>
        <p class="text-gray-400 text-sm flex-1">{course.description}</p>
        <div class="flex items-center justify-between mt-2">
          <div class="flex gap-3 text-sm text-gray-500">
            <span>⛳ {course.holes} holes</span>
          </div>
          <span class="text-xs px-2 py-1 rounded-full border {difficultyColor(course.difficulty)}">{course.difficulty}</span>
        </div>
      </a>
    {/each}
  </div>
</section>

<!-- Disc Spotlight -->
<section class="w-full px-8 py-16 bg-[#1a1d27] border-y border-white/10">
  <div class="flex items-end justify-between mb-8">
    <div>
      <p class="text-green-400 text-sm font-semibold tracking-widest uppercase mb-2">Essential Plastic</p>
      <h2 class="text-3xl font-bold text-white">Spotlight Discs</h2>
    </div>
    <a href="/discs" class="text-green-400 text-sm hover:text-green-300 transition-colors duration-200">View all →</a>
  </div>

  <div class="grid grid-cols-1 md:grid-cols-5 gap-4">
    {#each spotlightDiscs as disc}
      <a href="/discs" class="bg-[#0f1117] border border-white/10 rounded-2xl p-4 flex flex-col gap-2 hover:border-green-500/30 transition-colors duration-200">
        <div class="flex items-center justify-between">
          <span class="text-xl">{typeIcon(disc.type)}</span>
          <span class="text-xs {stabilityColor(disc.stability)}">{disc.stability}</span>
        </div>
        <p class="text-white font-semibold">{disc.name}</p>
        <p class="text-gray-500 text-xs">{disc.brand}</p>
        <div class="grid grid-cols-4 gap-1 mt-1 text-center">
          <div>
            <p class="text-gray-600 text-[10px]">Spd</p>
            <p class="text-white text-xs font-bold">{disc.speed}</p>
          </div>
          <div>
            <p class="text-gray-600 text-[10px]">Gli</p>
            <p class="text-white text-xs font-bold">{disc.glide}</p>
          </div>
          <div>
            <p class="text-gray-600 text-[10px]">Trn</p>
            <p class="text-white text-xs font-bold">{disc.turn}</p>
          </div>
          <div>
            <p class="text-gray-600 text-[10px]">Fde</p>
            <p class="text-white text-xs font-bold">{disc.fade}</p>
          </div>
        </div>
      </a>
    {/each}
  </div>
</section>

<!-- Feature cards -->
<section class="w-full px-8 py-16">
  <div class="mb-8">
    <p class="text-green-400 text-sm font-semibold tracking-widest uppercase mb-2">Everything You Need</p>
    <h2 class="text-3xl font-bold text-white">What's Inside</h2>
  </div>

  <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
    <a href="/courses" class="bg-[#1a1d27] border border-white/10 rounded-2xl p-6 hover:border-green-500/30 transition-colors duration-200">
      <div class="text-3xl mb-4">🗺️</div>
      <h3 class="text-white font-semibold mb-2">Course Finder</h3>
      <p class="text-gray-400 text-sm">Browse {courses.length} KC metro disc golf courses with ratings, difficulty, and amenities.</p>
    </a>

    <a href="/scorecard" class="bg-[#1a1d27] border border-white/10 rounded-2xl p-6 hover:border-green-500/30 transition-colors duration-200">
      <div class="text-3xl mb-4">📋</div>
      <h3 class="text-white font-semibold mb-2">Scorecard</h3>
      <p class="text-gray-400 text-sm">Log your rounds hole by hole with live birdie, par, and bogey tracking.</p>
    </a>

    <a href="/discs" class="bg-[#1a1d27] border border-white/10 rounded-2xl p-6 hover:border-green-500/30 transition-colors duration-200">
      <div class="text-3xl mb-4">🥏</div>
      <h3 class="text-white font-semibold mb-2">Disc Database</h3>
      <p class="text-gray-400 text-sm">Explore {discs.length} discs across {[...new Set(discs.map(d => d.brand))].length} brands with flight numbers and path diagrams.</p>
    </a>

    <a href="/bag" class="bg-[#1a1d27] border border-white/10 rounded-2xl p-6 hover:border-green-500/30 transition-colors duration-200">
      <div class="text-3xl mb-4">🎒</div>
      <h3 class="text-white font-semibold mb-2">Virtual Bag</h3>
      <p class="text-gray-400 text-sm">Build and save your disc bag with discs from the database.</p>
    </a>
  </div>
</section>

<!-- CTA -->
<section class="w-full px-8 py-16 text-center">
  <div class="bg-[#1a1d27] border border-white/10 rounded-2xl px-8 py-14 max-w-2xl mx-auto">
    <p class="text-5xl mb-4">🥏</p>
    <h2 class="text-3xl font-bold text-white mb-4">Ready to Play?</h2>
    <p class="text-gray-400 mb-8">Log your next round and start tracking your progress on the course.</p>
    <a href="/scorecard" class="bg-green-500 text-black font-semibold px-8 py-3 rounded-full text-lg hover:bg-green-400 transition-colors duration-200">
      Start Scorecard
    </a>
  </div>
</section>