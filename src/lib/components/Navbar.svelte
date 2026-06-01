<script>
  import { page } from '$app/stores';

const links = [
  { href: '/', label: 'Home', icon: '🏠' },
  { href: '/courses', label: 'Courses', icon: '🗺️' },
  { href: '/scorecard', label: 'Scorecard', icon: '📋' },
  { href: '/discs', label: 'Discs', icon: '🥏' },
  { href: '/bag', label: 'My Bag', icon: '🎒' },
  { href: '/about', label: 'About', icon: 'ℹ️' }
];
</script>

<header class="bg-[#1a1d27] border-b border-white/10 w-full">
  <div class="px-8 py-5 flex items-center justify-between">
    <a href="/" class="flex items-center gap-3">
      <div class="w-10 h-10 bg-green-500 rounded-xl flex items-center justify-center text-xl">
        🥏
      </div>
      <div>
        <p class="text-white font-bold text-lg leading-none">DiscGolf</p>
        <p class="text-gray-500 text-xs">Find your next round</p>
      </div>
    </a>

    <a
      href="https://github.com/djkephart"
      target="_blank"
      class="text-gray-400 hover:text-green-400 transition-colors duration-200 text-sm hidden md:block"
    >
      GitHub ↗
    </a>
  </div>

  <div class="px-8 pb-4 flex gap-2">
    {#each links as link}
      <a
        href={link.href}
        class="flex items-center gap-2 px-4 py-2 rounded-full text-sm font-medium transition-all duration-200
          {$page.url.pathname === link.href
            ? 'bg-green-500 text-black'
            : 'text-gray-400 hover:text-white hover:bg-white/10'}"
      >
        <span>{link.icon}</span>
        <span>{link.label}</span>
      </a>
    {/each}
  </div>
</header>