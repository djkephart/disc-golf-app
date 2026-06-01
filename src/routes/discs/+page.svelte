<script>
  import { discs } from '$lib/data/discs.js';

  const brands = ['All', ...new Set(discs.map(d => d.brand))];
  const types = ['All', 'Distance Driver', 'Fairway Driver', 'Midrange', 'Putter'];
  const stabilities = ['All', 'Very Understable', 'Understable', 'Neutral', 'Overstable', 'Very Overstable'];

  let search = $state('');
  let selectedBrand = $state('All');
  let selectedType = $state('All');
  let selectedStability = $state('All');
  let expandedId = $state(null);

  const filtered = $derived(() => {
    let result = [...discs];

    if (search.trim()) {
      const q = search.toLowerCase();
      result = result.filter(d =>
        d.name.toLowerCase().includes(q) ||
        d.brand.toLowerCase().includes(q)
      );
    }

    if (selectedBrand !== 'All') result = result.filter(d => d.brand === selectedBrand);
    if (selectedType !== 'All') result = result.filter(d => d.type === selectedType);
    if (selectedStability !== 'All') result = result.filter(d => d.stability === selectedStability);

    return result;
  });

  function toggle(id) {
    expandedId = expandedId === id ? null : id;
  }

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

  function getFlightPath(disc) {
    const w = 400;
    const h = 120;
    const startX = 20;
    const startY = h / 2;
    const endX = w - 20;

    // turn affects how much the disc curves right (negative = understable = curves right for RHBH)
    // fade affects how much the disc finishes left
    const turnAmount = disc.turn * -18;
    const fadeAmount = disc.fade * 15;
    const glideY = disc.glide * -4;

    const cp1x = w * 0.35;
    const cp1y = startY + glideY + turnAmount;
    const cp2x = w * 0.65;
    const cp2y = startY + glideY + turnAmount;
    const endY = startY + fadeAmount;

    return `M ${startX} ${startY} C ${cp1x} ${cp1y}, ${cp2x} ${cp2y}, ${endX} ${endY}`;
  }

  function addToBag(disc) {
    const bag = JSON.parse(localStorage.getItem('disc-bag') || '[]');
    if (!bag.find(d => d.id === disc.id)) {
      bag.push(disc);
      localStorage.setItem('disc-bag', JSON.stringify(bag));
      alert(`${disc.name} added to your bag!`);
    } else {
      alert(`${disc.name} is already in your bag.`);
    }
  }
</script>

<section class="px-8 py-12 w-full">
  <p class="text-green-400 text-sm font-semibold tracking-widest uppercase mb-2">Disc Database</p>
  <h1 class="text-4xl font-bold text-white mb-2">Discs</h1>
  <p class="text-gray-400 mb-8">Search and explore {discs.length} discs from the top brands in disc golf.</p>

  <!-- Search -->
  <div class="relative mb-6">
    <span class="absolute left-4 top-1/2 -translate-y-1/2 text-gray-500">🔍</span>
    <input
      type="text"
      placeholder="Search by disc name or brand..."
      bind:value={search}
      class="w-full bg-[#1a1d27] border border-white/10 rounded-full px-12 py-3 text-white placeholder-gray-500 focus:outline-none focus:border-green-500/50 transition-colors duration-200"
    />
    {#if search}
      <button onclick={() => search = ''} class="absolute right-4 top-1/2 -translate-y-1/2 text-gray-500 hover:text-white transition-colors">✕</button>
    {/if}
  </div>

  <!-- Type filter -->
  <div class="flex gap-2 mb-4 flex-wrap">
    {#each types as type}
      <button
        onclick={() => selectedType = type}
        class="flex items-center gap-1.5 px-4 py-2 rounded-full text-sm font-medium transition-all duration-200
          {selectedType === type ? 'bg-green-500 text-black' : 'bg-[#1a1d27] border border-white/10 text-gray-400 hover:text-white hover:border-white/30'}"
      >
        {#if type !== 'All'}{typeIcon(type)}{/if}
        {type}
      </button>
    {/each}
  </div>

  <!-- Brand filter -->
  <div class="flex gap-2 mb-4 flex-wrap">
    {#each brands as brand}
      <button
        onclick={() => selectedBrand = brand}
        class="px-3 py-1.5 rounded-full text-sm transition-all duration-200
          {selectedBrand === brand ? 'bg-white/20 text-white border border-white/30' : 'text-gray-500 hover:text-gray-300'}"
      >
        {brand}
      </button>
    {/each}
  </div>

  <!-- Stability filter -->
  <div class="flex gap-2 mb-8 flex-wrap">
    <span class="text-gray-500 text-sm self-center">Stability:</span>
    {#each stabilities as s}
      <button
        onclick={() => selectedStability = s}
        class="px-3 py-1.5 rounded-full text-sm transition-all duration-200
          {selectedStability === s ? 'bg-white/10 text-white border border-white/20' : 'text-gray-500 hover:text-gray-300'}"
      >
        {s}
      </button>
    {/each}
  </div>

  <!-- Results count -->
  <p class="text-gray-500 text-sm mb-6">{filtered().length} disc{filtered().length !== 1 ? 's' : ''} found</p>

  <!-- Disc list -->
  {#if filtered().length > 0}
    <div class="flex flex-col gap-3">
      {#each filtered() as disc}
        <div class="bg-[#1a1d27] border border-white/10 rounded-2xl overflow-hidden transition-all duration-200 hover:border-green-500/30">

          <!-- Disc header row -->
          <button
            onclick={() => toggle(disc.id)}
            class="w-full px-6 py-4 flex items-center justify-between text-left"
          >
            <div class="flex items-center gap-4">
              <span class="text-2xl">{typeIcon(disc.type)}</span>
              <div>
                <p class="text-white font-semibold">{disc.name}</p>
                <p class="text-gray-500 text-sm">{disc.brand} · {disc.type}</p>
              </div>
            </div>

            <div class="flex items-center gap-6">
              <div class="hidden md:flex gap-4 text-sm">
                <span class="text-gray-400">Spd <span class="text-white font-semibold">{disc.speed}</span></span>
                <span class="text-gray-400">Gli <span class="text-white font-semibold">{disc.glide}</span></span>
                <span class="text-gray-400">Trn <span class="text-white font-semibold">{disc.turn}</span></span>
                <span class="text-gray-400">Fde <span class="text-white font-semibold">{disc.fade}</span></span>
              </div>
              <span class="text-sm font-medium {stabilityColor(disc.stability)}">{disc.stability}</span>
              <span class="text-gray-500 text-sm">{expandedId === disc.id ? '▲' : '▼'}</span>
            </div>
          </button>

          <!-- Expanded detail -->
          {#if expandedId === disc.id}
            <div class="px-6 pb-6 border-t border-white/10 pt-4">
              <div class="grid grid-cols-1 md:grid-cols-2 gap-6">

                <!-- Left: info -->
                <div class="flex flex-col gap-4">
                  <p class="text-gray-400 text-sm">{disc.description}</p>

                  <div class="grid grid-cols-4 gap-3 text-center">
                    <div class="bg-[#0f1117] rounded-xl p-3">
                      <p class="text-gray-500 text-xs uppercase tracking-widest mb-1">Speed</p>
                      <p class="text-white font-bold text-xl">{disc.speed}</p>
                    </div>
                    <div class="bg-[#0f1117] rounded-xl p-3">
                      <p class="text-gray-500 text-xs uppercase tracking-widest mb-1">Glide</p>
                      <p class="text-white font-bold text-xl">{disc.glide}</p>
                    </div>
                    <div class="bg-[#0f1117] rounded-xl p-3">
                      <p class="text-gray-500 text-xs uppercase tracking-widest mb-1">Turn</p>
                      <p class="text-white font-bold text-xl">{disc.turn}</p>
                    </div>
                    <div class="bg-[#0f1117] rounded-xl p-3">
                      <p class="text-gray-500 text-xs uppercase tracking-widest mb-1">Fade</p>
                      <p class="text-white font-bold text-xl">{disc.fade}</p>
                    </div>
                  </div>

                  <p class="text-gray-500 text-sm">🧪 Plastic: <span class="text-gray-300">{disc.plastic}</span></p>

                  <div class="flex gap-3 mt-2">
                    <button
                      onclick={() => addToBag(disc)}
                      class="flex-1 bg-green-500 text-black font-semibold px-4 py-2.5 rounded-full hover:bg-green-400 transition-colors duration-200 text-sm"
                    >
                      + Add to Bag
                    </button>
                    <a
                      href={disc.url}
                      target="_blank"
                      class="flex-1 border border-white/10 text-gray-300 font-semibold px-4 py-2.5 rounded-full hover:bg-white/10 transition-colors duration-200 text-sm text-center"
                    >
                      View on Infinite Discs ↗
                    </a>
                  </div>
                </div>

                <!-- Right: flight path -->
                <div class="bg-[#0f1117] rounded-xl p-4">
                  <p class="text-gray-500 text-xs uppercase tracking-widest mb-3 text-center">Flight Path (RHBH)</p>
                  <svg viewBox="0 0 400 120" xmlns="http://www.w3.org/2000/svg" class="w-full">
                    <!-- Grid lines -->
                    <line x1="20" y1="60" x2="380" y2="60" stroke="#ffffff10" stroke-width="1"/>
                    <line x1="20" y1="20" x2="20" y2="100" stroke="#ffffff20" stroke-width="1"/>
                    <line x1="380" y1="20" x2="380" y2="100" stroke="#ffffff20" stroke-width="1"/>
                    <text x="15" y="115" fill="#666" font-size="10" text-anchor="middle">Tee</text>
                    <text x="385" y="115" fill="#666" font-size="10" text-anchor="middle">Basket</text>
                    <text x="200" y="12" fill="#666" font-size="9" text-anchor="middle">← Left / Right →</text>

                    <!-- Flight path -->
                    <path
                      d={getFlightPath(disc)}
                      fill="none"
                      stroke="#22c55e"
                      stroke-width="2.5"
                      stroke-linecap="round"
                    />

                    <!-- Start dot -->
                    <circle cx="20" cy="60" r="4" fill="#22c55e"/>
                    <!-- End dot -->
                    <circle cx="380" cy={60 + disc.fade * 15} r="4" fill="#22c55e" opacity="0.6"/>
                  </svg>
                </div>

              </div>
            </div>
          {/if}
        </div>
      {/each}
    </div>
  {:else}
    <div class="text-center py-20">
      <p class="text-4xl mb-4">🥏</p>
      <p class="text-white font-semibold mb-2">No discs found</p>
      <p class="text-gray-500 text-sm">Try adjusting your search or filters</p>
    </div>
  {/if}
</section>