<script>
  import { courses } from '$lib/data/courses.js';
  import { page } from '$app/stores';

  const preselectedId = Number($page.url.searchParams.get('course')) || null;

  let selectedCourseId = $state(preselectedId);
  let currentHole = $state(0);
  let scores = $state([]);
  let submitted = $state(false);

  const selectedCourse = $derived(courses.find(c => c.id === selectedCourseId));

  function selectCourse(id) {
    selectedCourseId = id;
    const course = courses.find(c => c.id === id);
    scores = Array.from({ length: course.holes }, (_, i) => ({
      hole: i + 1,
      par: 3,
      score: null
    }));
    currentHole = 0;
    submitted = false;
  }

  if (preselectedId) selectCourse(preselectedId);

  const totalScore = $derived(scores.reduce((acc, h) => acc + (h.score ?? 0), 0));
  const totalPar = $derived(scores.reduce((acc, h) => acc + h.par, 0));
  const scoreDiff = $derived(totalScore - totalPar);

  function setScore(val) {
    scores[currentHole].score = val;
  }

  function next() {
    if (currentHole < scores.length - 1) currentHole++;
    else submitted = true;
  }

  function prev() {
    if (currentHole > 0) currentHole--;
  }

  function getScoreLabel(score, par) {
    const diff = score - par;
    if (diff <= -2) return { label: 'Eagle', color: 'text-yellow-400' };
    if (diff === -1) return { label: 'Birdie', color: 'text-green-400' };
    if (diff === 0) return { label: 'Par', color: 'text-white' };
    if (diff === 1) return { label: 'Bogey', color: 'text-red-400' };
    if (diff === 2) return { label: 'Double Bogey', color: 'text-red-500' };
    return { label: `+${diff}`, color: 'text-red-600' };
  }
</script>

<section class="px-6 py-12 max-w-lg mx-auto">
  <p class="text-green-400 text-sm font-semibold tracking-widest uppercase mb-2">Log a Round</p>
  <h1 class="text-4xl font-bold text-white mb-8">Scorecard</h1>

  {#if !selectedCourseId}
    <p class="text-gray-400 mb-6">Select a course to get started:</p>
    <div class="flex flex-col gap-4">
      {#each courses as course}
        <button
          onclick={() => selectCourse(course.id)}
          class="bg-[#1a1d27] border border-white/10 rounded-2xl p-5 text-left hover:border-green-500/50 transition-colors duration-200"
        >
          <h2 class="text-white font-semibold mb-1">{course.name}</h2>
          <p class="text-green-400 text-sm">📍 {course.location}</p>
          <p class="text-gray-500 text-sm mt-1">⛳ {course.holes} holes · 📊 {course.difficulty}</p>
        </button>
      {/each}
    </div>

  {:else if submitted}
    <div class="bg-[#1a1d27] border border-white/10 rounded-2xl p-8 text-center flex flex-col gap-4">
      <div class="text-5xl">🥏</div>
      <h2 class="text-2xl font-bold text-white">Round Complete!</h2>
      <p class="text-gray-400">{selectedCourse.name}</p>

      <div class="grid grid-cols-3 gap-4 my-4">
        <div class="bg-[#0f1117] rounded-xl p-4">
          <p class="text-gray-400 text-xs uppercase tracking-widest mb-1">Score</p>
          <p class="text-white font-bold text-2xl">{totalScore}</p>
        </div>
        <div class="bg-[#0f1117] rounded-xl p-4">
          <p class="text-gray-400 text-xs uppercase tracking-widest mb-1">Par</p>
          <p class="text-white font-bold text-2xl">{totalPar}</p>
        </div>
        <div class="bg-[#0f1117] rounded-xl p-4">
          <p class="text-gray-400 text-xs uppercase tracking-widest mb-1">+/- Par</p>
          <p class="font-bold text-2xl {scoreDiff < 0 ? 'text-green-400' : scoreDiff === 0 ? 'text-white' : 'text-red-400'}">
            {scoreDiff > 0 ? '+' : ''}{scoreDiff}
          </p>
        </div>
      </div>

      <div class="bg-[#0f1117] rounded-xl p-4 text-left">
        <p class="text-gray-400 text-xs uppercase tracking-widest mb-3">Hole Summary</p>
        <div class="grid grid-cols-6 gap-2">
          {#each scores as hole}
            {@const diff = (hole.score ?? hole.par) - hole.par}
            <div class="rounded-lg p-2 text-center text-xs {diff < 0 ? 'bg-green-500/20 text-green-400' : diff === 0 ? 'bg-white/10 text-white' : 'bg-red-500/20 text-red-400'}">
              <p class="text-gray-500 text-[10px]">H{hole.hole}</p>
              <p class="font-bold">{hole.score ?? '—'}</p>
            </div>
          {/each}
        </div>
      </div>

      <button
        onclick={() => { selectedCourseId = null; submitted = false; }}
        class="bg-green-500 text-black font-semibold px-6 py-3 rounded-full hover:bg-green-400 transition-colors duration-200"
      >
        Log Another Round
      </button>
    </div>

  {:else}
    <!-- Progress bar -->
    <div class="mb-8">
      <div class="flex justify-between text-sm text-gray-500 mb-2">
        <span>{selectedCourse.name}</span>
        <span>Hole {currentHole + 1} of {scores.length}</span>
      </div>
      <div class="w-full bg-white/10 rounded-full h-1.5">
        <div
          class="bg-green-500 h-1.5 rounded-full transition-all duration-300"
          style="width: {((currentHole + 1) / scores.length) * 100}%"
        ></div>
      </div>
    </div>

    <!-- Hole card -->
    <div class="bg-[#1a1d27] border border-white/10 rounded-2xl p-8 mb-6">
      <div class="flex items-center justify-between mb-6">
        <div>
          <p class="text-gray-500 text-sm uppercase tracking-widest">Hole</p>
          <p class="text-6xl font-bold text-white">{currentHole + 1}</p>
        </div>
        <div class="text-right">
          <p class="text-gray-500 text-sm uppercase tracking-widest">Par</p>
          <div class="flex items-center gap-2 justify-end mt-1">
            <button
              onclick={() => scores[currentHole].par = Math.max(3, scores[currentHole].par - 1)}
              class="w-7 h-7 rounded-full bg-white/10 text-white hover:bg-white/20 transition-colors"
            >−</button>
            <p class="text-3xl font-bold text-white w-8 text-center">{scores[currentHole].par}</p>
            <button
              onclick={() => scores[currentHole].par = Math.min(6, scores[currentHole].par + 1)}
              class="w-7 h-7 rounded-full bg-white/10 text-white hover:bg-white/20 transition-colors"
            >+</button>
          </div>
        </div>
      </div>

      <!-- Score selector -->
      <p class="text-gray-500 text-sm uppercase tracking-widest mb-4 text-center">Your Score</p>
      <div class="flex justify-center gap-3 flex-wrap">
        {#each [1, 2, 3, 4, 5, 6, 7, 8] as val}
          {@const diff = val - scores[currentHole].par}
          <button
            onclick={() => setScore(val)}
            class="w-14 h-14 rounded-2xl font-bold text-lg transition-all duration-150
              {scores[currentHole].score === val
                ? diff < 0 ? 'bg-green-500 text-black' : diff === 0 ? 'bg-white text-black' : 'bg-red-500 text-white'
                : 'bg-white/10 text-white hover:bg-white/20'}"
          >
            {val}
          </button>
        {/each}
      </div>

      {#if scores[currentHole].score !== null}
        {@const label = getScoreLabel(scores[currentHole].score, scores[currentHole].par)}
        <p class="text-center mt-4 font-semibold {label.color}">{label.label}</p>
      {/if}
    </div>

    <!-- Running total -->
    <div class="grid grid-cols-3 gap-3 mb-6 text-center">
      <div class="bg-[#1a1d27] border border-white/10 rounded-xl p-3">
        <p class="text-gray-500 text-xs uppercase tracking-widest mb-1">Score</p>
        <p class="text-white font-bold">{totalScore || '—'}</p>
      </div>
      <div class="bg-[#1a1d27] border border-white/10 rounded-xl p-3">
        <p class="text-gray-500 text-xs uppercase tracking-widest mb-1">Par</p>
        <p class="text-white font-bold">{totalPar}</p>
      </div>
      <div class="bg-[#1a1d27] border border-white/10 rounded-xl p-3">
        <p class="text-gray-500 text-xs uppercase tracking-widest mb-1">+/- Par</p>
        <p class="font-bold {scoreDiff < 0 ? 'text-green-400' : scoreDiff === 0 ? 'text-white' : 'text-red-400'}">
          {totalScore > 0 ? (scoreDiff > 0 ? '+' : '') + scoreDiff : '—'}
        </p>
      </div>
    </div>

    <!-- Navigation -->
    <div class="flex gap-4">
      {#if currentHole > 0}
        <button
          onclick={prev}
          class="flex-1 border border-white/10 text-white font-semibold px-6 py-3 rounded-full hover:bg-white/10 transition-colors duration-200"
        >
          ← Previous
        </button>
      {/if}
      <button
        onclick={next}
        disabled={scores[currentHole].score === null}
        class="flex-1 bg-green-500 text-black font-semibold px-6 py-3 rounded-full hover:bg-green-400 transition-colors duration-200 disabled:opacity-40 disabled:cursor-not-allowed"
      >
        {currentHole === scores.length - 1 ? 'Finish Round' : 'Next Hole →'}
      </button>
    </div>

    <button
      onclick={() => { selectedCourseId = null; }}
      class="w-full text-gray-600 text-sm mt-4 hover:text-gray-400 transition-colors"
    >
      Change Course
    </button>
  {/if}
</section>