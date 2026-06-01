<script>
  import { onMount } from 'svelte';

  let rounds = $state([]);
  let expandedId = $state(null);
  let confirmClear = $state(false);
  let confirmDeleteId = $state(null);

  onMount(() => {
    rounds = JSON.parse(localStorage.getItem('round-history') || '[]');
  });

  function deleteRound(id) {
    rounds = rounds.filter(r => r.id !== id);
    localStorage.setItem('round-history', JSON.stringify(rounds));
    confirmDeleteId = null;
  }

  function clearHistory() {
    rounds = [];
    localStorage.setItem('round-history', JSON.stringify([]));
    confirmClear = false;
  }

  function toggle(id) {
    expandedId = expandedId === id ? null : id;
  }

  function scoreColor(diff) {
    if (diff < 0) return 'text-green-400';
    if (diff === 0) return 'text-white';
    return 'text-red-400';
  }

  function holeScoreColor(score, par) {
    const diff = score - par;
    if (diff <= -2) return 'bg-yellow-400/20 text-yellow-400';
    if (diff === -1) return 'bg-green-500/20 text-green-400';
    if (diff === 0) return 'bg-white/10 text-white';
    if (diff === 1) return 'bg-red-500/20 text-red-400';
    return 'bg-red-700/20 text-red-500';
  }

  const averageScore = $derived(() => {
    if (rounds.length === 0) return null;
    const avg = rounds.reduce((acc, r) => acc + r.scoreDiff, 0) / rounds.length;
    return Math.round(avg * 10) / 10;
  });

  const bestRound = $derived(() => {
    if (rounds.length === 0) return null;
    return rounds.reduce((best, r) => r.scoreDiff < best.scoreDiff ? r : best);
  });
</script>

<section class="px-8 py-12 w-full">
  <div class="flex items-center justify-between mb-2">
    <p class="text-green-400 text-sm font-semibold tracking-widest uppercase">Round History</p>
    {#if rounds.length > 0}
      {#if confirmClear}
        <div class="flex items-center gap-3">
          <span class="text-gray-400 text-sm">Are you sure?</span>
          <button onclick={clearHistory} class="text-red-400 text-sm hover:text-red-300 transition-colors">Yes, clear</button>
          <button onclick={() => confirmClear = false} class="text-gray-500 text-sm hover:text-gray-300 transition-colors">Cancel</button>
        </div>
      {:else}
        <button onclick={() => confirmClear = true} class="text-gray-500 text-sm hover:text-red-400 transition-colors duration-200">
          Clear History
        </button>
      {/if}
    {/if}
  </div>

  <h1 class="text-4xl font-bold text-white mb-8">My Rounds</h1>

  {#if rounds.length === 0}
    <div class="text-center py-24">
      <p class="text-5xl mb-4">📋</p>
      <p class="text-white font-semibold text-lg mb-2">No rounds logged yet</p>
      <p class="text-gray-500 text-sm mb-8">Complete a scorecard to see your round history here.</p>
      <a
        href="/scorecard"
        class="bg-green-500 text-black font-semibold px-6 py-3 rounded-full hover:bg-green-400 transition-colors duration-200"
      >
        Log a Round
      </a>
    </div>

  {:else}
    <!-- Stats summary -->
    <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mb-10">
      <div class="bg-[#1a1d27] border border-white/10 rounded-2xl p-5 text-center">
        <p class="text-gray-500 text-xs uppercase tracking-widest mb-1">Rounds Played</p>
        <p class="text-white font-bold text-3xl">{rounds.length}</p>
      </div>
      <div class="bg-[#1a1d27] border border-white/10 rounded-2xl p-5 text-center">
        <p class="text-gray-500 text-xs uppercase tracking-widest mb-1">Avg Score</p>
        <p class="font-bold text-3xl {scoreColor(averageScore())}">
          {averageScore() > 0 ? '+' : ''}{averageScore()}
        </p>
      </div>
      <div class="bg-[#1a1d27] border border-white/10 rounded-2xl p-5 text-center">
        <p class="text-gray-500 text-xs uppercase tracking-widest mb-1">Best Round</p>
        <p class="font-bold text-3xl {scoreColor(bestRound()?.scoreDiff)}">
          {bestRound()?.scoreDiff > 0 ? '+' : ''}{bestRound()?.scoreDiff}
        </p>
      </div>
      <div class="bg-[#1a1d27] border border-white/10 rounded-2xl p-5 text-center">
        <p class="text-gray-500 text-xs uppercase tracking-widest mb-1">Courses Played</p>
        <p class="text-white font-bold text-3xl">{new Set(rounds.map(r => r.course)).size}</p>
      </div>
    </div>

    <!-- Round list -->
    <div class="flex flex-col gap-3">
      {#each rounds as round}
        <div class="bg-[#1a1d27] border border-white/10 rounded-2xl overflow-hidden hover:border-green-500/30 transition-colors duration-200">

          <!-- Round header -->
          <button
            onclick={() => toggle(round.id)}
            class="w-full px-6 py-4 flex items-center justify-between text-left"
          >
            <div class="flex items-center gap-4">
              <div class="w-10 h-10 bg-[#0f1117] rounded-xl flex items-center justify-center text-lg">🥏</div>
              <div>
                <p class="text-white font-semibold">{round.course}</p>
                <p class="text-gray-500 text-sm">📍 {round.location} · {round.date}</p>
              </div>
            </div>

            <div class="flex items-center gap-6">
              <div class="text-right hidden md:block">
                <p class="text-gray-500 text-xs uppercase tracking-widest">Score</p>
                <p class="text-white font-bold">{round.totalScore} / {round.totalPar}</p>
              </div>
              <div class="text-right">
                <p class="text-gray-500 text-xs uppercase tracking-widest">+/- Par</p>
                <p class="font-bold text-lg {scoreColor(round.scoreDiff)}">
                  {round.scoreDiff > 0 ? '+' : ''}{round.scoreDiff}
                </p>
              </div>
              <span class="text-gray-500 text-sm">{expandedId === round.id ? '▲' : '▼'}</span>
            </div>
          </button>

          <!-- Expanded hole breakdown -->
          {#if expandedId === round.id}
            <div class="px-6 pb-6 border-t border-white/10 pt-4">
              <p class="text-gray-500 text-xs uppercase tracking-widest mb-4">Hole by Hole</p>

              <div class="grid grid-cols-6 md:grid-cols-9 gap-2 mb-6">
                {#each round.scores as hole}
                  <div class="rounded-xl p-2 text-center {holeScoreColor(hole.score, hole.par)}">
                    <p class="text-[10px] opacity-60">H{hole.hole}</p>
                    <p class="font-bold text-sm">{hole.score}</p>
                    <p class="text-[10px] opacity-60">p{hole.par}</p>
                  </div>
                {/each}
              </div>

              {#if confirmDeleteId === round.id}
                <div class="flex items-center gap-3">
                  <span class="text-gray-400 text-sm">Delete this round?</span>
                  <button onclick={() => deleteRound(round.id)} class="text-red-400 text-sm hover:text-red-300 transition-colors">Yes, delete</button>
                  <button onclick={() => confirmDeleteId = null} class="text-gray-500 text-sm hover:text-gray-300 transition-colors">Cancel</button>
                </div>
              {:else}
                <button onclick={() => confirmDeleteId = round.id} class="text-gray-600 text-sm hover:text-red-400 transition-colors duration-200">
                  Delete Round
                </button>
              {/if}
            </div>
          {/if}
        </div>
      {/each}
    </div>
  {/if}
</section>