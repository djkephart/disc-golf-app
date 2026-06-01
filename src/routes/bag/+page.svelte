<script>
  import { onMount } from 'svelte';

  let bag = $state([]);

  let confirmClear = $state(false);

  onMount(() => {
    bag = JSON.parse(localStorage.getItem('disc-bag') || '[]');
  });

  function removeFromBag(id) {
    bag = bag.filter(d => d.id !== id);
    localStorage.setItem('disc-bag', JSON.stringify(bag));
  }

  function clearBag() {
    bag = [];
    localStorage.setItem('disc-bag', JSON.stringify([]));
  }

  const types = ['Distance Driver', 'Fairway Driver', 'Midrange', 'Putter'];

  function typeIcon(type) {
    if (type === 'Distance Driver') return '💨';
    if (type === 'Fairway Driver') return '🎯';
    if (type === 'Midrange') return '🔄';
    if (type === 'Putter') return '🥅';
    return '🥏';
  }

  function stabilityColor(stability) {
    if (stability === 'Very Understable') return 'text-blue-400';
    if (stability === 'Understable') return 'text-cyan-400';
    if (stability === 'Neutral') return 'text-green-400';
    if (stability === 'Overstable') return 'text-orange-400';
    if (stability === 'Very Overstable') return 'text-red-400';
    return 'text-gray-400';
  }

  const discsInBag = $derived(bag.length);
</script>

<section class="px-8 py-12 w-full">
  <div class="flex items-center justify-between mb-2">
    <p class="text-green-400 text-sm font-semibold tracking-widest uppercase">My Bag</p>
    {#if bag.length > 0}
    {#if confirmClear}
        <div class="flex items-center gap-3">
        <span class="text-gray-400 text-sm">Are you sure?</span>
        <button onclick={clearBag} class="text-red-400 text-sm hover:text-red-300 transition-colors">Yes, clear</button>
        <button onclick={() => confirmClear = false} class="text-gray-500 text-sm hover:text-gray-300 transition-colors">Cancel</button>
        </div>
    {:else}
        <button onclick={() => confirmClear = true} class="text-gray-500 text-sm hover:text-red-400 transition-colors duration-200">
        Clear Bag
        </button>
    {/if}
    {/if}
  </div>

  <h1 class="text-4xl font-bold text-white mb-2">Disc Bag</h1>
  <p class="text-gray-400 mb-10">
    {discsInBag} disc{discsInBag !== 1 ? 's' : ''} in your bag
  </p>

  {#if bag.length === 0}
    <div class="text-center py-24">
      <p class="text-5xl mb-4">🎒</p>
      <p class="text-white font-semibold text-lg mb-2">Your bag is empty</p>
      <p class="text-gray-500 text-sm mb-8">Head to the disc database to add some plastic.</p>
      <a
        href="/discs"
        class="bg-green-500 text-black font-semibold px-6 py-3 rounded-full hover:bg-green-400 transition-colors duration-200"
      >
        Browse Discs
      </a>
    </div>

  {:else}
    <div class="flex flex-col gap-10">
      {#each types as type}
        {@const discsOfType = bag.filter(d => d.type === type)}
        {#if discsOfType.length > 0}
          <div>
            <div class="flex items-center gap-3 mb-4">
              <span class="text-2xl">{typeIcon(type)}</span>
              <h2 class="text-xl font-bold text-white">{type}s</h2>
              <span class="text-gray-500 text-sm">{discsOfType.length} disc{discsOfType.length !== 1 ? 's' : ''}</span>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
              {#each discsOfType as disc}
                <div class="bg-[#1a1d27] border border-white/10 rounded-2xl p-5 flex flex-col gap-3 hover:border-green-500/30 transition-colors duration-200">
                  <div class="flex items-start justify-between">
                    <div>
                      <p class="text-white font-semibold">{disc.name}</p>
                      <p class="text-gray-500 text-sm">{disc.brand}</p>
                    </div>
                    <button
                      onclick={() => removeFromBag(disc.id)}
                      class="text-gray-600 hover:text-red-400 transition-colors duration-200 text-lg leading-none"
                    >
                      ✕
                    </button>
                  </div>

                  <div class="grid grid-cols-4 gap-2 text-center">
                    <div class="bg-[#0f1117] rounded-lg p-2">
                      <p class="text-gray-500 text-[10px] uppercase tracking-widest">Spd</p>
                      <p class="text-white font-bold">{disc.speed}</p>
                    </div>
                    <div class="bg-[#0f1117] rounded-lg p-2">
                      <p class="text-gray-500 text-[10px] uppercase tracking-widest">Gli</p>
                      <p class="text-white font-bold">{disc.glide}</p>
                    </div>
                    <div class="bg-[#0f1117] rounded-lg p-2">
                      <p class="text-gray-500 text-[10px] uppercase tracking-widest">Trn</p>
                      <p class="text-white font-bold">{disc.turn}</p>
                    </div>
                    <div class="bg-[#0f1117] rounded-lg p-2">
                      <p class="text-gray-500 text-[10px] uppercase tracking-widest">Fde</p>
                      <p class="text-white font-bold">{disc.fade}</p>
                    </div>
                  </div>

                  <div class="flex items-center justify-between">
                    <span class="text-sm font-medium {stabilityColor(disc.stability)}">{disc.stability}</span>
                    <a
                      href={disc.url}
                      target="_blank"
                      class="text-gray-500 text-sm hover:text-green-400 transition-colors duration-200"
                    >
                      View ↗
                    </a>
                  </div>
                </div>
              {/each}
            </div>
          </div>
        {/if}
      {/each}
    </div>
  {/if}
</section>